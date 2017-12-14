---
title: "Authentification de service à service Azure AD à l’aide de la spécification préliminaire Pour le compte de OAuth2.0 | Microsoft Docs"
description: "Cet article explique comment utiliser des messages HTTP pour implémenter l’authentification de service à service en utilisant le flux Pour le compte de OAuth 2.0."
services: active-directory
documentationcenter: .net
author: navyasric
manager: mtillman
editor: 
ms.assetid: 09f6f318-e88b-4024-9ee1-e7f09fb19a82
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2017
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: bb3e01b1b8741253a459a41cfff27da558573551
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="service-to-service-calls-using-delegated-user-identity-in-the-on-behalf-of-flow"></a>Appels service à service utilisant l’identité utilisateur déléguée dans le flux Pour le compte de
Le flux Pour le compte de OAuth 2.0 sert quand une application appelle un service/API web, qui à son tour doit appeler un autre service/API web. L’idée est de propager l’identité et les autorisations de l’utilisateur délégué via la chaîne de la demande. Pour que le service de niveau intermédiaire puisse faire des demandes authentifiées au service en aval, il doit sécuriser un jeton d’accès d’Azure Active Directory (Azure AD) pour le compte de l’utilisateur.

## <a name="on-behalf-of-flow-diagram"></a>Diagramme du flux Pour le compte de
Supposons que l’utilisateur a été authentifié sur une application à l’aide du [flux d’octroi de code d’autorisation OAuth 2.0](active-directory-protocols-oauth-code.md). À ce stade, l’application a un jeton d’accès (jeton A) avec les revendications et le consentement de l’utilisateur pour accéder à l’API web de niveau intermédiaire (API A). L’API A doit maintenant faire une demande authentifiée à l’API web en aval (API B).

Les étapes qui suivent constituent le flux Pour le compte de et sont décrites à l’aide du diagramme suivant.

![Flux Pour le compte de OAuth 2.0](media/active-directory-protocols-oauth-on-behalf-of/active-directory-protocols-oauth-on-behalf-of-flow.png)


1. L’application cliente fait une demande à l’API A avec le jeton A.
2. L’API A s’authentifie auprès du point de terminaison d’émission de jeton Azure AD et demande un jeton pour accéder à l’API B.
3. Le point de terminaison d’émission de jeton Azure AD valide les informations d’identification de l’API A avec le jeton A et émet le jeton d’accès pour l’API B (jeton B).
4. Le jeton B est défini dans l’en-tête d’autorisation de la demande adressée à l’API B.
5. Les données de la ressource sécurisée sont retournées par l’API B.

## <a name="register-the-application-and-service-in-azure-ad"></a>Inscrire le service et l’application dans Azure AD
Inscrivez l’application cliente et le service de niveau intermédiaire dans Azure AD.
### <a name="register-the-middle-tier-service"></a>Inscrire le service de niveau intermédiaire
1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Dans la barre supérieure, cliquez sur votre compte et, dans la liste **Répertoire**, choisissez le locataire Active Directory auprès duquel vous voulez inscrire votre application.
3. Cliquez sur **Autres services** dans le volet de navigation gauche et choisissez **Azure Active Directory**.
4. Cliquez sur **Inscriptions des applications** et choisissez **Nouvelle inscription d’application**.
5. Entrez un nom convivial pour l’application, puis sélectionnez le type d’application. En fonction du type d’application, définissez l’URL de connexion ou l’URL de redirection sur l’URL de base. Cliquez sur **Créer** pour créer l’application.
6. Toujours dans le portail Azure, choisissez votre application, puis cliquez sur **Paramètres**. Dans le menu Paramètres, choisissez **Clés**, puis ajoutez une clé. Sélectionnez une durée de clé d’une ou deux années. Lorsque vous enregistrez cette page, la valeur de clé s’affiche. Copiez et enregistrez cette valeur dans un emplacement sûr, car vous en aurez besoin plus tard pour configurer les paramètres d’application dans votre implémentation. Cette valeur de clé ne s’affichera plus et ne pourra pas être récupérée, par conséquent, enregistrez-la dès que vous la voyez dans le portail Azure.

### <a name="register-the-client-application"></a>Inscrire l’application cliente
1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Dans la barre supérieure, cliquez sur votre compte et, dans la liste **Répertoire**, choisissez le locataire Active Directory auprès duquel vous voulez inscrire votre application.
3. Cliquez sur **Autres services** dans le volet de navigation gauche et choisissez **Azure Active Directory**.
4. Cliquez sur **Inscriptions des applications** et choisissez **Nouvelle inscription d’application**.
5. Entrez un nom convivial pour l’application, puis sélectionnez le type d’application. En fonction du type d’application, définissez l’URL de connexion ou l’URL de redirection sur l’URL de base. Cliquez sur **Créer** pour créer l’application.
6. Configurez les autorisations pour votre application : dans le menu Paramètres, cliquez sur la section **Autorisations requises**, cliquez sur **Ajouter**, cliquez sur **Sélectionner une API**, puis tapez le nom du service de niveau intermédiaire dans la zone de texte. Ensuite, cliquez sur **Sélectionner les autorisations**, puis sélectionnez Accéder à *nom du service*.

### <a name="configure-known-client-applications"></a>Configurer les applications clientes connues
Dans ce scénario, le service de niveau intermédiaire n’a aucune interaction utilisateur pour obtenir le consentement de l’utilisateur pour accéder à l’API en aval. Par conséquent, l’option d’accorder l’accès à l’API en aval doit être présentée au préalable lors de l’étape de consentement pendant l’authentification.
Pour ce faire, suivez les étapes ci-dessous pour lier de manière explicite l’inscription de l’application cliente dans Azure AD à l’inscription du service de niveau intermédiaire. Ceci a pour effet de fusionner le consentement exigé à la fois par le client et par le niveau intermédiaire dans une même boîte de dialogue.
1. Accédez à l’inscription du service de niveau intermédiaire, puis cliquez sur **Manifeste** pour ouvrir l’éditeur de manifeste.
2. Dans le manifeste, localisez la propriété de tableau `knownClientApplications`, puis ajoutez l’ID client de l’application cliente en tant qu’élément.
3. Enregistrez le manifeste en cliquant sur le bouton Enregistrer.

## <a name="service-to-service-access-token-request"></a>Demande de jeton d’accès de service à service
Pour demander un jeton d’accès, faites une demande HTTP POST au point de terminaison Azure AD spécifique au locataire, avec les paramètres suivants.

```
https://login.microsoftonline.com/<tenant>/oauth2/token
```
Deux cas de figure se présentent, selon que l’application cliente choisit d’être sécurisée par un secret partagé ou un certificat.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Premier cas : demande de jeton d’accès avec un secret partagé
Lorsque l’application utilise un secret partagé, la demande de jeton d’accès de service à service contient les paramètres suivants :

| Paramètre |  | Description |
| --- | --- | --- |
| grant_type |required | Type de la demande de jeton. Pour une demande à l’aide d’un JWT, la valeur doit être **urn:ietf:params:oauth:grant-type:jwt-bearer**. |
| assertion |required | Valeur du jeton utilisé dans la demande. |
| client_id |required | ID d’application affecté au service appelant lors de l’inscription auprès d’Azure AD. Pour rechercher l’ID d’application, dans le Portail de gestion Azure, cliquez successivement sur **Active Directory**, sur le répertoire, puis sur le nom de l’application. |
| client_secret |required | Clé enregistrée pour le service appelant dans Azure AD. Vous devez avoir noté cette valeur au moment de l’inscription. |
| resource |required | URI ID d’application du service web de destination (ressource sécurisée). Pour rechercher l’URI ID d’application, dans le portail de gestion Azure, cliquez successivement sur **Active Directory**, sur le répertoire, sur le nom de l’application, sur **Tous les paramètres**, puis sur **Propriétés**. |
| requested_token_use |required | Spécifie comment la demande doit être traitée. Dans le flux Pour le compte de, la valeur doit être **on_behalf_of**. |
| scope |required | Liste des étendues (séparées par des espaces) pour la demande de jeton. Pour OpenID Connect, l’étendue **openid** doit être spécifiée.|

#### <a name="example"></a>Exemple
La requête HTTP POST suivante demande un jeton d’accès pour l’API web https://graph.windows.net. `client_id` identifie le service qui demande le jeton d’accès.

```
// line breaks for legibility only

POST /oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Ajwt-bearer
&client_id=625391af-c675-43e5-8e44-edd3e30ceb15
&client_secret=0Y1W%2BY3yYb3d9N8vSjvm8WrGzVZaAaHbHHcGbcgG%2BoI%3D
&resource=https%3A%2F%2Fgraph.windows.net
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2Rkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tLzE5MjNmODYyLWU2ZGMtNDFhMy04MWRhLTgwMmJhZTAwYWY2ZCIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzI2MDM5Y2NlLTQ4OWQtNDAwMi04MjkzLTViMGM1MTM0ZWFjYi8iLCJpYXQiOjE0OTM0MjMxNTIsIm5iZiI6MTQ5MzQyMzE1MiwiZXhwIjoxNDkzNDY2NjUyLCJhY3IiOiIxIiwiYWlvIjoiWTJaZ1lCRFF2aTlVZEc0LzM0L3dpQndqbjhYeVp4YmR1TFhmVE1QeG8yYlN2elgreHBVQSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiJiMzE1MDA3OS03YmViLTQxN2YtYTA2YS0zZmRjNzhjMzI1NDUiLCJhcHBpZGFjciI6IjAiLCJlX2V4cCI6MzAyNDAwLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJEVXpYbkdKMDJIUk0zRW5pbDFxdjZCakxTNUllQy0tQ2ZpbzRxS1MzNEc4IiwidGlkIjoiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwidW5pcXVlX25hbWUiOiJuYXZ5YUBkZG9iYWxpYW5vdXRsb29rLm9ubWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidmVyIjoiMS4wIn0.R-Ke-XO7lK0r5uLwxB8g5CrcPAwRln5SccJCfEjU6IUqpqcjWcDzeDdNOySiVPDU_ZU5knJmzRCF8fcjFtPsaA4R7vdIEbDuOur15FXSvE8FvVSjP_49OH6hBYqoSUAslN3FMfbO6Z8YfCIY4tSOB2I6ahQ_x4ZWFWglC3w5mK-_4iX81bqi95eV4RUKefUuHhQDXtWhrSgIEC0YiluMvA4TnaJdLq_tWXIc4_Tq_KfpkvI004ONKgU7EAMEr1wZ4aDcJV2yf22gQ1sCSig6EGSTmmzDuEPsYiyd4NhidRZJP4HiiQh-hePBQsgcSgYGvz9wC6n57ufYKh2wm_Ti3Q
&requested_token_use=on_behalf_of
&scope=openid
```

### <a name="second-case-access-token-request-with-a-certificate"></a>Deuxième cas : demande de jeton d’accès avec un certificat
Une demande de jeton d’accès de service à service avec un certificat contient les paramètres suivants :

| Paramètre |  | Description |
| --- | --- | --- |
| grant_type |required | Type de la demande de jeton. Pour une demande à l’aide d’un JWT, la valeur doit être **urn:ietf:params:oauth:grant-type:jwt-bearer**. |
| assertion |required | Valeur du jeton utilisé dans la demande. |
| client_id |required | ID d’application affecté au service appelant lors de l’inscription auprès d’Azure AD. Pour rechercher l’ID d’application, dans le Portail de gestion Azure, cliquez successivement sur **Active Directory**, sur le répertoire, puis sur le nom de l’application. |
| client_assertion_type |required |La valeur doit être `urn:ietf:params:oauth:client-assertion-type:jwt-bearer`. |
| client_assertion |required | Assertion (JSON Web Token) dont vous avez besoin pour créer et signer avec le certificat inscrit comme informations d’identification pour votre application.  Pour découvrir comment inscrire votre certificat et le format de l’assertion, consultez la section traitant des [informations d’identification des certificats](active-directory-certificate-credentials.md).|
| resource |required | URI ID d’application du service web de destination (ressource sécurisée). Pour rechercher l’URI ID d’application, dans le portail de gestion Azure, cliquez successivement sur **Active Directory**, sur le répertoire, sur le nom de l’application, sur **Tous les paramètres**, puis sur **Propriétés**. |
| requested_token_use |required | Spécifie comment la demande doit être traitée. Dans le flux Pour le compte de, la valeur doit être **on_behalf_of**. |
| scope |required | Liste des étendues (séparées par des espaces) pour la demande de jeton. Pour OpenID Connect, l’étendue **openid** doit être spécifiée.|

Notez que les paramètres sont presque les mêmes que dans le cas de la demande par secret partagé, sauf que le paramètre client_secret est remplacé par deux paramètres : client_assertion_type et client_assertion.

#### <a name="example"></a>Exemple
La requête HTTP POST suivante demande un jeton d’accès pour l’API web https://graph.windows.net avec un certificat. `client_id` identifie le service qui demande le jeton d’accès.

```
// line breaks for legibility only

POST /oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Ajwt-bearer
&client_id=625391af-c675-43e5-8e44-edd3e30ceb15
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&resource=https%3A%2F%2Fgraph.windows.net
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2Rkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tLzE5MjNmODYyLWU2ZGMtNDFhMy04MWRhLTgwMmJhZTAwYWY2ZCIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzI2MDM5Y2NlLTQ4OWQtNDAwMi04MjkzLTViMGM1MTM0ZWFjYi8iLCJpYXQiOjE0OTM0MjMxNTIsIm5iZiI6MTQ5MzQyMzE1MiwiZXhwIjoxNDkzNDY2NjUyLCJhY3IiOiIxIiwiYWlvIjoiWTJaZ1lCRFF2aTlVZEc0LzM0L3dpQndqbjhYeVp4YmR1TFhmVE1QeG8yYlN2elgreHBVQSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiJiMzE1MDA3OS03YmViLTQxN2YtYTA2YS0zZmRjNzhjMzI1NDUiLCJhcHBpZGFjciI6IjAiLCJlX2V4cCI6MzAyNDAwLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJEVXpYbkdKMDJIUk0zRW5pbDFxdjZCakxTNUllQy0tQ2ZpbzRxS1MzNEc4IiwidGlkIjoiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwidW5pcXVlX25hbWUiOiJuYXZ5YUBkZG9iYWxpYW5vdXRsb29rLm9ubWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidmVyIjoiMS4wIn0.R-Ke-XO7lK0r5uLwxB8g5CrcPAwRln5SccJCfEjU6IUqpqcjWcDzeDdNOySiVPDU_ZU5knJmzRCF8fcjFtPsaA4R7vdIEbDuOur15FXSvE8FvVSjP_49OH6hBYqoSUAslN3FMfbO6Z8YfCIY4tSOB2I6ahQ_x4ZWFWglC3w5mK-_4iX81bqi95eV4RUKefUuHhQDXtWhrSgIEC0YiluMvA4TnaJdLq_tWXIc4_Tq_KfpkvI004ONKgU7EAMEr1wZ4aDcJV2yf22gQ1sCSig6EGSTmmzDuEPsYiyd4NhidRZJP4HiiQh-hePBQsgcSgYGvz9wC6n57ufYKh2wm_Ti3Q
&requested_token_use=on_behalf_of
&scope=openid
```

## <a name="service-to-service-access-token-response"></a>Réponse de jeton d’accès de service à service
Une réponse correspondant à une réussite est une réponse JSON OAuth 2.0 avec les paramètres suivants.

| Paramètre | Description |
| --- | --- |
| token_type |Indique la valeur du type de jeton. Le seul type de jeton pris en charge par Azure AD est le **jeton porteur**. Pour plus d’informations sur les jetons du porteur, consultez [OAuth 2.0 Authorization Framework: Bearer Token Usage (RFC 6750)](http://www.rfc-editor.org/rfc/rfc6750.txt). |
| scope |Étendue de l’accès accordé dans le jeton. |
| expires_in |Durée de validité du jeton d’accès (en secondes). |
| expires_on |L’heure d’expiration du jeton d’accès. La date est représentée en nombre de secondes à partir du 1er janvier 1970 (1970-01-01T0:0:0Z) UTC jusqu’au moment de l’expiration. Cette valeur est utilisée pour déterminer la durée de vie des jetons en cache. |
| resource |URI ID d’application du service web de destination (ressource sécurisée). |
| access_token |Le jeton d’accès demandé. Le service web appelant peut utiliser ce jeton pour s’authentifier auprès du service destinataire. |
| id_token |Jeton d’ID demandé. Le service appelant peut utiliser le jeton d’ID pour vérifier l’identité de l’utilisateur et démarrer une session avec lui. |
| refresh_token |Jeton d’actualisation pour le jeton d’accès demandé. Le service appelant peut utiliser ce jeton pour demander un autre jeton d’accès après l’expiration du jeton d’accès actuel. |

### <a name="success-response-example"></a>Exemple de réponse correspondant à une réussite
L’exemple suivant montre une réponse correspondant à une réussite à une demande de jeton d’accès pour l’API web https://graph.windows.net.

```
{
    "token_type":"Bearer",
    "scope":"User.Read",
    "expires_in":"43482",
    "ext_expires_in":"302683",
    "expires_on":"1493466951",
    "not_before":"1493423168",
    "resource":"https://graph.windows.net",
    "access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRvd3MubmV0IiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiLyIsImlhdCI6MTQ5MzQyMzE2OCwibmJmIjoxNDkzNDIzMTY4LCJleHAiOjE0OTM0NjY5NTEsImFjciI6IjEiLCJhaW8iOiJBU1FBMi84REFBQUE1NnZGVmp0WlNjNWdBVWwrY1Z0VFpyM0VvV2NvZEoveWV1S2ZqcTZRdC9NPSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiI2MjUzOTFhZi1jNjc1LTQzZTUtOGU0NC1lZGQzZTMwY2ViMTUiLCJhcHBpZGFjciI6IjEiLCJlX2V4cCI6MzAyNjgzLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJwdWlkIjoiMTAwMzNGRkZBMTJFRDdGRSIsInNjcCI6IlVzZXIuUmVhZCIsInN1YiI6IjNKTUlaSWJlYTc1R2hfWHdDN2ZzX0JDc3kxa1l1ekZKLTUyVm1Zd0JuM3ciLCJ0aWQiOiIyNjAzOWNjZS00ODlkLTQwMDItODI5My01YjBjNTEzNGVhY2IiLCJ1bmlxdWVfbmFtZSI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidXBuIjoibmF2eWFAZGRvYmFsaWFub3V0bG9vay5vbm1pY3Jvc29mdC5jb20iLCJ1dGkiOiJ4Q3dmemhhLVAwV0pRT0x4Q0dnS0FBIiwidmVyIjoiMS4wIn0.cqmUVjfVbqWsxJLUI1Z4FRx1mNQAHP-L0F4EMN09r8FY9bIKeO-0q1eTdP11Nkj_k4BmtaZsTcK_mUygdMqEp9AfyVyA1HYvokcgGCW_Z6DMlVGqlIU4ssEkL9abgl1REHElPhpwBFFBBenOk9iHddD1GddTn6vJbKC3qAaNM5VarjSPu50bVvCrqKNvFixTb5bbdnSz-Qr6n6ACiEimiI1aNOPR2DeKUyWBPaQcU5EAK0ef5IsVJC1yaYDlAcUYIILMDLCD9ebjsy0t9pj_7lvjzUSrbMdSCCdzCqez_MSNxrk1Nu9AecugkBYp3UVUZOIyythVrj6-sVvLZKUutQ",
    "refresh_token":"AQABAAAAAABnfiG-mA6NTae7CdWW7QfdjKGu9-t1scy_TDEmLi4eLQMjJGt_nAoVu6A4oSu1KsRiz8XyQIPKQxSGfbf2FoSK-hm2K8TYzbJuswYusQpJaHUQnSqEvdaCeFuqXHBv84wjFhuanzF9dQZB_Ng5za9xKlUENrNtlq9XuLNVKzxEyeUM7JyxzdY7JiEphWImwgOYf6II316d0Z6-H3oYsFezf4Xsjz-MOBYEov0P64UaB5nJMvDyApV-NWpgklLASfNoSPGb67Bc02aFRZrm4kLk-xTl6eKE6hSo0XU2z2t70stFJDxvNQobnvNHrAmBaHWPAcC3FGwFnBOojpZB2tzG1gLEbmdROVDp8kHEYAwnRK947Py12fJNKExUdN0njmXrKxNZ_fEM33LHW1Tf4kMX_GvNmbWHtBnIyG0w5emb-b54ef5AwV5_tGUeivTCCysgucEc-S7G8Cz0xNJ_BOiM_4bAv9iFmrm9STkltpz0-Tftg8WKmaJiC0xXj6uTf4ZkX79mJJIuuM7XP4ARIcLpkktyg2Iym9jcZqymRkGH2Rm9sxBwC4eeZXM7M5a7TJ-5CqOdfuE3sBPq40RdEWMFLcrAzFvP0VDR8NKHIrPR1AcUruat9DETmTNJukdlJN3O41nWdZOVoJM-uKN3uz2wQ2Ld1z0Mb9_6YfMox9KTJNzRzcL52r4V_y3kB6ekaOZ9wQ3HxGBQ4zFt-2U0mSszIAA",
    "id_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiI2MjUzOTFhZi1jNjc1LTQzZTUtOGU0NC1lZGQzZTMwY2ViMTUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC8yNjAzOWNjZS00ODlkLTQwMDItODI5My01YjBjNTEzNGVhY2IvIiwiaWF0IjoxNDkzNDIzMTY4LCJuYmYiOjE0OTM0MjMxNjgsImV4cCI6MTQ5MzQ2Njk1MSwiYW1yIjpbInB3ZCJdLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJzdWIiOiJEVXpYbkdKMDJIUk0zRW5pbDFxdjZCakxTNUllQy0tQ2ZpbzRxS1MzNEc4IiwidGlkIjoiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwidW5pcXVlX25hbWUiOiJuYXZ5YUBkZG9iYWxpYW5vdXRsb29rLm9ubWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidXRpIjoieEN3ZnpoYS1QMFdKUU9MeENHZ0tBQSIsInZlciI6IjEuMCJ9."
}
```

### <a name="error-response-example"></a>Exemple de réponse d’erreur
Une réponse d’erreur est retournée par le point de terminaison du jeton Azure AD lors de la tentative d’acquérir un jeton d’accès pour l’API en aval si une stratégie d’accès conditionnel comme l’authentification multifacteur est définie sur cette API. Le service de niveau intermédiaire doit faire apparaître cette erreur à l’application cliente afin que celle-ci puisse fournir une interaction utilisateur pour satisfaire la stratégie d’accès conditionnel.

```
{
    "error":"interaction_required",
    "error_description":"AADSTS50079: Due to a configuration change made by your administrator, or because you moved to a new location, you must enroll in multi-factor authentication to access 'bf8d80f9-9098-4972-b203-500f535113b1'.\r\nTrace ID: b72a68c3-0926-4b8e-bc35-3150069c2800\r\nCorrelation ID: 73d656cf-54b1-4eb2-b429-26d8165a52d7\r\nTimestamp: 2017-05-01 22:43:20Z",
    "error_codes":[50079],
    "timestamp":"2017-05-01 22:43:20Z",
    "trace_id":"b72a68c3-0926-4b8e-bc35-3150069c2800",
    "correlation_id":"73d656cf-54b1-4eb2-b429-26d8165a52d7",
    "claims":"{\"access_token\":{\"polids\":{\"essential\":true,\"values\":[\"9ab03e19-ed42-4168-b6b7-7001fb3e933a\"]}}}"
}
```

## <a name="use-the-access-token-to-access-the-secured-resource"></a>Utiliser le jeton d’accès pour accéder à la ressource sécurisée
Le service de niveau intermédiaire peut maintenant utiliser le jeton obtenu ci-dessus pour faire des demandes authentifiées à l’API web en aval, en définissant le jeton dans l’en-tête `Authorization`.

### <a name="example"></a>Exemple
```
GET /me?api-version=2013-11-08 HTTP/1.1
Host: graph.windows.net
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRvd3MubmV0IiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiLyIsImlhdCI6MTQ5MzQyMzE2OCwibmJmIjoxNDkzNDIzMTY4LCJleHAiOjE0OTM0NjY5NTEsImFjciI6IjEiLCJhaW8iOiJBU1FBMi84REFBQUE1NnZGVmp0WlNjNWdBVWwrY1Z0VFpyM0VvV2NvZEoveWV1S2ZqcTZRdC9NPSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiI2MjUzOTFhZi1jNjc1LTQzZTUtOGU0NC1lZGQzZTMwY2ViMTUiLCJhcHBpZGFjciI6IjEiLCJlX2V4cCI6MzAyNjgzLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJwdWlkIjoiMTAwMzNGRkZBMTJFRDdGRSIsInNjcCI6IlVzZXIuUmVhZCIsInN1YiI6IjNKTUlaSWJlYTc1R2hfWHdDN2ZzX0JDc3kxa1l1ekZKLTUyVm1Zd0JuM3ciLCJ0aWQiOiIyNjAzOWNjZS00ODlkLTQwMDItODI5My01YjBjNTEzNGVhY2IiLCJ1bmlxdWVfbmFtZSI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidXBuIjoibmF2eWFAZGRvYmFsaWFub3V0bG9vay5vbm1pY3Jvc29mdC5jb20iLCJ1dGkiOiJ4Q3dmemhhLVAwV0pRT0x4Q0dnS0FBIiwidmVyIjoiMS4wIn0.cqmUVjfVbqWsxJLUI1Z4FRx1mNQAHP-L0F4EMN09r8FY9bIKeO-0q1eTdP11Nkj_k4BmtaZsTcK_mUygdMqEp9AfyVyA1HYvokcgGCW_Z6DMlVGqlIU4ssEkL9abgl1REHElPhpwBFFBBenOk9iHddD1GddTn6vJbKC3qAaNM5VarjSPu50bVvCrqKNvFixTb5bbdnSz-Qr6n6ACiEimiI1aNOPR2DeKUyWBPaQcU5EAK0ef5IsVJC1yaYDlAcUYIILMDLCD9ebjsy0t9pj_7lvjzUSrbMdSCCdzCqez_MSNxrk1Nu9AecugkBYp3UVUZOIyythVrj6-sVvLZKUutQ
```

## <a name="next-steps"></a>Étapes suivantes
Découvrez plus d’informations sur le protocole OAuth 2.0 et une autre méthode pour effectuer l’authentification de service à service à l’aide des informations d’identification du client.
* [Authentification de service à service utilisant l’octroi d’informations d’identification du client OAuth 2.0 dans Azure AD](active-directory-protocols-oauth-service-to-service.md)
* [OAuth 2.0 dans Azure AD](active-directory-protocols-oauth-code.md)
