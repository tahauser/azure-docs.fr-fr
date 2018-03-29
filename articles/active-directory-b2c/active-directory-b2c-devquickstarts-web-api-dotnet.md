---
title: Appeler une API web sécurisée ASP.NET Azure Active Directory B2C | Microsoft Docs
description: Comment créer une application web .NET et appeler une API web à l’aide d’Azure Active Directory B2C et des jetons d’accès OAuth 2.0.
services: active-directory-b2c
documentationcenter: .net
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 03/17/2017
ms.author: davidmu
ms.openlocfilehash: 75ae88898d3d6b275ec462e865347056305fd666
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-ad-b2c-call-a-net-web-api-from-a-net-web-app"></a>Azure AD B2C : appel d’une API web .NET à partir d’une application web .NET

Avec Azure AD B2C, vous pouvez ajouter à vos applications web et vos API web des fonctionnalités de gestion des identités performantes. Cet article explique comment demander des jetons d’accès et effectuer des appels d’une application .NET de type « liste de tâches » vers une API web .NET.

Cet article ne couvre pas l'implémentation de la connexion, de l'inscription et de la gestion de profil avec Azure AD B2C. Il s'intéresse principalement à l'appel d'API web après que l'utilisateur s'est authentifié. Si vous ne l’avez pas déjà fait, consultez :

* Bien démarrer avec une [application web .NET](active-directory-b2c-devquickstarts-web-dotnet-susi.md)
* Bien démarrer avec une [API web .NET](active-directory-b2c-devquickstarts-api-dotnet.md)

## <a name="prerequisite"></a>Configuration requise

Pour créer une application web qui appelle une API web, procédez comme suit :

1. [Créez un locataire Azure AD B2C](active-directory-b2c-get-started.md).
2. [Inscrivez une API web](active-directory-b2c-app-registration.md#register-a-web-api).
3. [Inscrivez une application web](active-directory-b2c-app-registration.md#register-a-web-app).
4. [Configurez des stratégies](active-directory-b2c-reference-policies.md).
5. [Accordez à l’application web des autorisations d’utilisation de l’API web](active-directory-b2c-access-tokens.md#publishing-permissions).

> [!IMPORTANT]
> L’application cliente et l’API web doivent utiliser le même répertoire Azure AD B2C.
>

## <a name="download-the-code"></a>Téléchargement du code

Le code associé à ce didacticiel est stocké sur [GitHub](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi). Vous pouvez cloner l’exemple en exécutant :

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

Une fois l’exemple de code téléchargé, ouvrez le fichier .sln Visual Studio pour commencer. Le fichier solution contient deux projets : `TaskWebApp` et `TaskService`. `TaskWebApp` est une application web MVC avec laquelle l’utilisateur interagit. `TaskService` est l’API web du serveur principal de l’application qui stocke la liste de tâches de chaque utilisateur. Cet article ne couvre pas la création de l’application web `TaskWebApp` ou de l’API web `TaskService`. Pour savoir comment générer une application web .NET à l’aide d’Azure AD B2C, consultez [notre didacticiel sur les applications web .NET](active-directory-b2c-devquickstarts-web-dotnet-susi.md). Pour savoir comment générer l’API web .NET sécurisée à l’aide d’Azure AD B2C, consultez [notre tutoriel sur les API web .NET](active-directory-b2c-devquickstarts-api-dotnet.md).

### <a name="update-the-azure-ad-b2c-configuration"></a>Mettre à jour la configuration Azure AD B2C

Notre exemple est configuré pour utiliser les stratégies et l’ID client du locataire de notre démonstration. Si vous souhaitez utiliser votre propre locataire, procédez comme suit :

1. Ouvrez `web.config` dans le projet `TaskService` et remplacez les valeurs de

    * `ida:Tenant` par le nom de votre locataire
    * `ida:ClientId` par votre ID d’application d’API web
    * `ida:SignUpSignInPolicyId` par votre nom de stratégie d’inscription ou de connexion

2. Ouvrez `web.config` dans le projet `TaskWebApp` et remplacez les valeurs de

    * `ida:Tenant` par le nom de votre locataire
    * `ida:ClientId` par votre ID d’application web
    * `ida:ClientSecret` par votre clé secrète d’application web
    * `ida:SignUpSignInPolicyId` par votre nom de stratégie d’inscription ou de connexion
    * `ida:EditProfilePolicyId` par votre nom de stratégie « Modifier le profil »
    * `ida:ResetPasswordPolicyId` par votre nom de stratégie « Modifier le mot de passe »



## <a name="requesting-and-saving-an-access-token"></a>Demande et enregistrement d’un jeton d’accès

### <a name="specify-the-permissions"></a>Spécifier les autorisations

Pour effectuer un appel à l’API web, vous devez authentifier l’utilisateur (à l’aide de votre stratégie d’inscription/de connexion) et [recevoir un jeton d’accès](active-directory-b2c-access-tokens.md) d’Azure AD B2C. Pour recevoir un jeton d’accès, vous devez d’abord spécifier les autorisations que vous souhaitez que le jeton d’accès accorde. Les autorisations sont spécifiées dans le paramètre `scope` lorsque vous effectuez la demande au point de terminaison `/authorize`. Par exemple, pour acquérir un jeton d’accès avec l’autorisation de « lecture » de l’application de ressource associée a l’URI ID d’application `https://contoso.onmicrosoft.com/tasks`, l’étendue est de `https://contoso.onmicrosoft.com/tasks/read`.

Pour spécifier l’étendue dans notre exemple, ouvrez le fichier `App_Start\Startup.Auth.cs` et définissez la variable `Scope` OpenIdConnectAuthenticationOptions.

```CSharp
// App_Start\Startup.Auth.cs

    app.UseOpenIdConnectAuthentication(
        new OpenIdConnectAuthenticationOptions
        {
            ...

            // Specify the scope by appending all of the scopes requested into one string (seperated by a blank space)
            Scope = $"{OpenIdConnectScopes.OpenId} {ReadTasksScope} {WriteTasksScope}"
        }
    );
}
```

### <a name="exchange-the-authorization-code-for-an-access-token"></a>Échanger le code d’autorisation contre un jeton d’accès

Une fois qu’un utilisateur a répondu à l’expérience d’inscription ou de connexion, votre application reçoit un code d’autorisation d’Azure AD B2C. Le middleware OWIN OpenID Connect stocke le code, mais ne l’échange pas contre un jeton d’accès. Vous pouvez utiliser la [bibliothèque MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) pour effectuer l’échange. Dans l’exemple, vous avez configuré un rappel de notification dans le middleware OpenID Connect chaque fois qu’un code d’autorisation a été reçu. Dans le rappel, vous utilisez MSAL pour échanger le code contre un jeton et enregistrer le jeton dans le cache.

```CSharp
/*
* Callback function when an authorization code is received
*/
private async Task OnAuthorizationCodeReceived(AuthorizationCodeReceivedNotification notification)
{
    // Extract the code from the response notification
    var code = notification.Code;

    var userObjectId = notification.AuthenticationTicket.Identity.FindFirst(ObjectIdElement).Value;
    var authority = String.Format(AadInstance, Tenant, DefaultPolicy);
    var httpContext = notification.OwinContext.Environment["System.Web.HttpContextBase"] as HttpContextBase;

    // Exchange the code for a token. Make sure to specify the necessary scopes
    ClientCredential cred = new ClientCredential(ClientSecret);
    ConfidentialClientApplication app = new ConfidentialClientApplication(authority, Startup.ClientId,
                                            RedirectUri, cred, new NaiveSessionCache(userObjectId, httpContext));
    var authResult = await app.AcquireTokenByAuthorizationCodeAsync(new string[] { ReadTasksScope, WriteTasksScope }, code, DefaultPolicy);
}
```

## <a name="calling-the-web-api"></a>Appel de l’API web

Cette section traite de l’utilisation du jeton reçu pendant l’inscription/la connexion avec Azure AD B2C afin d’accéder à l’API web.

### <a name="retrieve-the-saved-token-in-the-controllers"></a>Récupération du jeton enregistré dans les contrôleurs

`TasksController` est responsable de la communication avec l’API web et envoie des demandes HTTP à celle-ci pour lire, créer et supprimer des tâches. Puisque l’API est sécurisée par Azure AD B2C, vous devez d’abord récupérer le jeton que vous avez enregistré à l’étape précédente.

```CSharp
// Controllers\TasksController.cs

/*
* Uses MSAL to retrieve the token from the cache
*/
private async void acquireToken(String[] scope)
{
    string userObjectID = ClaimsPrincipal.Current.FindFirst(Startup.ObjectIdElement).Value;
    string authority = String.Format(Startup.AadInstance, Startup.Tenant, Startup.DefaultPolicy);

    ClientCredential credential = new ClientCredential(Startup.ClientSecret);

    // Retrieve the token using the provided scopes
    ConfidentialClientApplication app = new ConfidentialClientApplication(authority, Startup.ClientId,
                                        Startup.RedirectUri, credential,
                                        new NaiveSessionCache(userObjectID, this.HttpContext));
    AuthenticationResult result = await app.AcquireTokenSilentAsync(scope);

    accessToken = result.Token;
}
```

### <a name="read-tasks-from-the-web-api"></a>Lecture de tâches à partir de l'API Web

Quand vous avez un jeton, vous pouvez le joindre à la demande `GET` HTTP dans l’en-tête `Authorization` pour appeler `TaskService` en toute sécurité :

```CSharp
// Controllers\TasksController.cs

public async Task<ActionResult> Index()
{
    try {

        // Retrieve the token with the specified scopes
        acquireToken(new string[] { Startup.ReadTasksScope });

        HttpClient client = new HttpClient();
        HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, apiEndpoint);

        // Add token to the Authorization header and make the request
        request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);
        HttpResponseMessage response = await client.SendAsync(request);

        // Handle response ...
}

```

### <a name="create-and-delete-tasks-on-the-web-api"></a>Création et suppression de tâches dans l’API web

Suivez le même modèle lorsque vous envoyez les demandes `POST` et `DELETE` à l’API web, à l’aide de MSAL pour récupérer le jeton d’accès dans le cache.

## <a name="run-the-sample-app"></a>Exécution de l'exemple d'application

Pour terminer, générez et exécutez les deux applications. Inscrivez-vous et connectez-vous et créez des tâches pour l’utilisateur connecté. Déconnectez-vous et connectez-vous en tant qu’autre utilisateur. Créer des tâches pour cet utilisateur. Notez la façon dont les tâches sont stockées par utilisateur sur l’API, car l’API extrait l’identité de l’utilisateur à partir du jeton qu’elle reçoit. Essayez également d’utiliser les étendues. Supprimez l’autorisation d’« écriture » et essayez d’ajouter une tâche. Veillez simplement à vous déconnecter à chaque fois que vous modifiez l’étendue.

