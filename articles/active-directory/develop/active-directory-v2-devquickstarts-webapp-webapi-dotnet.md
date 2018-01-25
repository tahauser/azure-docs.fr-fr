---
title: "Prise en main de l’appel d’API dans les applications web Azure AD v2.0 .NET | Microsoft Docs"
description: "Comment créer une application Web .NET MVC qui appelle des services Web à l’aide de comptes personnels Microsoft et de comptes professionnels ou scolaires pour la connexion."
services: active-directory
documentationcenter: .net
author: dstrockis
manager: mtillman
editor: 
ms.assetid: 56be906e-71de-469d-9a5c-9fc08aae4223
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: f59c9e2c523db319565c1cca13eb85f809b2bdd6
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
# <a name="calling-a-web-api-from-a-net-web-app"></a>Appeler une API web à partir d’une application web .NET
Avec le point de terminaison v2.0, vous pouvez rapidement ajouter une authentification à vos applications Web et à vos API Web, avec prise en charge pour les comptes Microsoft personnels, ainsi que pour les comptes professionnels ou scolaires.  Ici, nous allons créer une application web MVC qui connecte les utilisateurs à l’aide d’OpenID Connect, avec l’aide de l’intergiciel OWIN de Microsoft.  L’application web obtiendra des jetons d’accès OAuth 2.0 pour une API web sécurisée par OAuth 2.0 qui permet d’exécuter des opérations de création, lecture et suppression dans la « To Do List » (Liste des tâches) d’un utilisateur.

Ce didacticiel s’intéresse principalement à l’utilisation de la bibliothèque MSAL pour récupérer et utiliser des jetons d’accès dans une application Web, dont la description complète est disponible [ici](active-directory-v2-flows.md#web-apps).  Afin de vous renseigner sur la configuration requise, apprenez dans un premier temps comment [ajouter une connexion de base à une application Web](active-directory-v2-devquickstarts-dotnet-web.md) ou comment [sécuriser une application Web de manière appropriée](active-directory-v2-devquickstarts-dotnet-api.md).

> [!NOTE]
> Les scénarios et les fonctionnalités Azure Active Directory ne sont pas tous pris en charge par le point de terminaison v2.0.  Pour déterminer si vous devez utiliser le point de terminaison v2.0, consultez les [limites de v2.0](active-directory-v2-limitations.md).
> 
> 

## <a name="download-sample-code"></a>Télécharger l’exemple de code
Le code associé à ce didacticiel est stocké [sur GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet).  Pour suivre la procédure, vous pouvez [télécharger la structure de l’application au format .zip](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet/archive/skeleton.zip) ou la cloner :

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet.git```

Vous pouvez également [télécharger l'application terminée dans un fichier zip](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet/archive/complete.zip) ou cloner l'application terminée :

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet.git```

## <a name="register-an-app"></a>Inscription d’une application
Créez une application à l’adresse [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), ou suivez cette [procédure détaillée](active-directory-v2-app-registration.md).  Veillez à respecter les points suivants :

* copier l' **ID d'application** attribué à votre application, vous en aurez bientôt besoin ;
* créer un **secret d’application** du type **Mot de passe**, puis copiez sa valeur pour une utilisation ultérieure.
* ajouter la plateforme **web** pour votre application ;
* entrer l’ **URI de redirection**approprié. L’URI de redirection indique à Azure AD où les réponses d’authentification doivent être dirigées. La valeur par défaut pour ce didacticiel est `https://localhost:44326/`.

## <a name="install-owin"></a>Installer OWIN
Ajoutez au projet `TodoList-WebApp` les packages NuGet de l’intergiciel OWIN à l’aide de la console du gestionnaire de package.  L’intergiciel OWIN sera utilisé pour émettre des demandes de connexion et de déconnexion, gérer la session utilisateur et obtenir des informations concernant l’utilisateur, entre autres.

```
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect -ProjectName TodoList-WebApp
PM> Install-Package Microsoft.Owin.Security.Cookies -ProjectName TodoList-WebApp
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoList-WebApp
```

## <a name="sign-the-user-in"></a>Connexion de l’utilisateur
À présent, configurez l’intergiciel OWIN pour utiliser le [protocole d’authentification OpenID Connect](active-directory-v2-protocols.md).  

* Ouvrez le fichier `web.config` à la racine du projet `TodoList-WebApp`, puis entrez les valeurs de configuration de votre application dans la section `<appSettings>`.
  * L’élément `ida:ClientId` est l’ **ID d’application** affecté à votre application dans le portail d’inscription.
  * L’élément `ida:ClientSecret` est le **secret d’application** que vous avez créé dans le portail d’inscription.
  * L’élément `ida:RedirectUri` est l’ **URI de redirection** que vous avez saisi dans le portail.
* Ouvrez le fichier `web.config` dans la racine du projet `TodoList-Service`, puis remplacez l’élément `ida:Audience` par **l’ID d’application** ci-dessus.
* Ouvrez le fichier `App_Start\Startup.Auth.cs`, puis ajoutez les instructions `using` pour les bibliothèques ci-dessus.
* Dans le même fichier, implémentez la méthode `ConfigureAuth(...)` .  Les paramètres que vous fournissez dans `OpenIDConnectAuthenticationOptions` serviront de coordonnées pour que votre application puisse communiquer avec Azure AD.

```csharp
public void ConfigureAuth(IAppBuilder app)
{
    app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

    app.UseCookieAuthentication(new CookieAuthenticationOptions());

    app.UseOpenIdConnectAuthentication(
        new OpenIdConnectAuthenticationOptions
        {

                    // The `Authority` represents the v2.0 endpoint - https://login.microsoftonline.com/common/v2.0
                    // The `Scope` describes the permissions that your app will need.  See https://azure.microsoft.com/documentation/articles/active-directory-v2-scopes/
                    // In a real application you could use issuer validation for additional checks, like making sure the user's organization has signed up for your app, for instance.

                    ClientId = clientId,
                    Authority = String.Format(CultureInfo.InvariantCulture, aadInstance, "common", "/v2.0 "),
                    Scope = "openid email profile offline_access",
                    RedirectUri = redirectUri,
                    PostLogoutRedirectUri = redirectUri,
                    TokenValidationParameters = new TokenValidationParameters
                    {
                        ValidateIssuer = false,
                    },

                    // The `AuthorizationCodeReceived` notification is used to capture and redeem the authorization_code that the v2.0 endpoint returns to your app.

                    Notifications = new OpenIdConnectAuthenticationNotifications
                    {
                        AuthenticationFailed = OnAuthenticationFailed,
                        AuthorizationCodeReceived = OnAuthorizationCodeReceived,
                    }

        });
}
// ...
```

## <a name="use-msal-to-get-access-tokens"></a>Utilisation de la bibliothèque MSAL pour obtenir des jetons d’accès
Dans la notification `AuthorizationCodeReceived`, nous souhaitons utiliser [OAuth 2.0 en tandem avec OpenID Connect](active-directory-v2-protocols.md) afin d’échanger le code d’autorisation contre un jeton d’accès au service de la liste de tâches.  La bibliothèque MSAL peut vous faciliter ce processus :

* Tout d’abord, installez la version d’évaluation de MSAL :

```PM> Install-Package Microsoft.Identity.Client -ProjectName TodoList-WebApp -IncludePrerelease```

* Puis, ajoutez une autre instruction `using` au fichier `App_Start\Startup.Auth.cs` pour MSAL.
* Vous pouvez alors ajouter une nouvelle méthode, le gestionnaire d’événements `OnAuthorizationCodeReceived`.  Ce gestionnaire utilisera MSAL pour acquérir un jeton d’accès à l’API To-Do List et le stockera dans le cache de jetons MSAL pour une utilisation ultérieure :

```csharp
private async Task OnAuthorizationCodeReceived(AuthorizationCodeReceivedNotification notification)
{
        string userObjectId = notification.AuthenticationTicket.Identity.FindFirst("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier").Value;
        string tenantID = notification.AuthenticationTicket.Identity.FindFirst("http://schemas.microsoft.com/identity/claims/tenantid").Value;
        string authority = String.Format(CultureInfo.InvariantCulture, aadInstance, tenantID, string.Empty);
        ClientCredential cred = new ClientCredential(clientId, clientSecret);

        // Here you ask for a token using the web app's clientId as the scope, since the web app and service share the same clientId.
        app = new ConfidentialClientApplication(Startup.clientId, redirectUri, cred, new NaiveSessionCache(userObjectId, notification.OwinContext.Environment["System.Web.HttpContextBase"] as HttpContextBase)) {};
        var authResult = await app.AcquireTokenByAuthorizationCodeAsync(new string[] { clientId }, notification.Code);
}
// ...
```

* Dans les applications Web, MSAL dispose d’un cache de jeton extensible pouvant être utilisé pour stocker les jetons.  Cet exemple implémente l’élément `NaiveSessionCache` qui utilise le stockage de session HTTP pour mettre en cache les jetons.

<!-- TODO: Token Cache article -->


## <a name="call-the-web-api"></a>Appel de l’API web
Il est à présent temps d’utiliser le jeton d’accès acquis lors de l’étape 3.  Ouvrez le fichier `Controllers\TodoListController.cs` de l’application web, qui exécute l’ensemble des requêtes CRUD sur l’API To-Do List.

* Vous pouvez réutiliser MSAL afin de récupérer les jetons d’accès du cache MSAL.  Tout d’abord, ajoutez une instruction `using` pour MSAL dans ce fichier.
  
    `using Microsoft.Identity.Client;`
* Dans l’action `Index`, utilisez la méthode `AcquireTokenSilentAsync` de MSAL pour obtenir un jeton d’accès pouvant être utilisé pour lire les données du service de la liste des tâches :

```csharp
// ...
string userObjectID = ClaimsPrincipal.Current.FindFirst("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier").Value;
string tenantID = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/tenantid").Value;
string authority = String.Format(CultureInfo.InvariantCulture, Startup.aadInstance, tenantID, string.Empty);
ClientCredential credential = new ClientCredential(Startup.clientId, Startup.clientSecret);

// Here you ask for a token using the web app's clientId as the scope, since the web app and service share the same clientId.
app = new ConfidentialClientApplication(Startup.clientId, redirectUri, credential, new NaiveSessionCache(userObjectID, this.HttpContext)){};
result = await app.AcquireTokenSilentAsync(new string[] { Startup.clientId });
// ...
```

* L’exemple ajoute ensuite le jeton obtenu à la requête GET HTTP en tant qu’en-tête `Authorization`, que le service de la liste des tâches utilise pour authentifier la requête.
* Si le service To-Do List renvoie une réponse `401 Unauthorized`, les jetons d’accès de MSAL deviennent non valides, pour une raison indéterminée.  Dans ce cas, vous devez abandonner les jetons d’accès du cache MSAL et transmettre à l’utilisateur un message lui demandant de se reconnecter. Le cas échéant, le flux d’acquisition des jetons est redémarré.

```csharp
// ...
// If the call failed with access denied, then drop the current access token from the cache,
// and show the user an error indicating they might need to sign-in again.
if (response.StatusCode == System.Net.HttpStatusCode.Unauthorized)
{
        app.AppTokenCache.Clear(Startup.clientId);

        return new RedirectResult("/Error?message=Error: " + response.ReasonPhrase + " You might need to sign in again.");
}
// ...
```

* De la même manière, si MSAL est dans l’impossibilité de renvoyer un jeton d’accès, pour quelque raison que ce soit, vous devez demander à l’utilisateur de se reconnecter.  Cette opération n’est pas plus compliquée que la récupération d’un élément `MSALException` :

```csharp
// ...
catch (MsalException ee)
{
        // If MSAL could not get a token silently, show the user an error indicating they might need to sign in again.
        return new RedirectResult("/Error?message=An Error Occurred Reading To Do List: " + ee.Message + " You might need to log out and log back in.");
}
// ...
```

* Le même appel `AcquireTokenSilentAsync` est implémenté dans les actions `Create` et `Delete`.  Dans les applications Web, vous pouvez utiliser cette méthode MSAL pour obtenir les jetons d’accès lorsque vous en avez besoin dans votre application.  MSAL se charge de l’acquisition, de la mise en cache et de l’actualisation des jetons.

Enfin, générez et exécutez votre application.  Connectez-vous avec un compte Microsoft ou un compte Azure AD, et prenez note du mode d’indication de l’identité de l’utilisateur dans la barre de navigation supérieure.  Ajoutez et supprimez certains éléments de la liste de tâches de l’utilisateur afin d’observer les appels d’API sécurisés OAuth 2.0 en action.  Vous disposez désormais d’une application Web et d’une API Web, toutes deux sécurisées à l’aide de protocoles standard et pouvant authentifier les utilisateurs avec leurs comptes personnels et professionnels/scolaires.

Pour référence, l’exemple terminé (sans vos valeurs de configuration) [est fourni ici](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet/archive/complete.zip).  

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir des ressources supplémentaires, consultez :

* [Guide du développeur 2.0 &gt;&gt;](active-directory-appmodel-v2-overview.md)
* [Balise StackOverflow "azure-active-directory" &gt;&gt;](http://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="get-security-updates-for-our-products"></a>Obtenir les mises à jour de sécurité de nos produits
Nous vous encourageons à activer les notifications d’incidents de sécurité en vous rendant sur [cette page](https://technet.microsoft.com/security/dd252948) et en vous abonnant aux alertes d’avis de sécurité.

