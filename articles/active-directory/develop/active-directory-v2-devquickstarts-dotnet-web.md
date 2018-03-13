---
title: Prise en main de la connexion aux applications web Azure AD v2.0 .NET | Microsoft Docs
description: "Génération d’une application web .NET MVC qui connecte les utilisateurs à l’aide de leur compte Microsoft personnel et de leur compte professionnel ou scolaire."
services: active-directory
documentationcenter: .net
author: dstrockis
manager: mtillman
editor: 
ms.assetid: c8b97ac6-0a06-4367-81b6-7d1d98152b14
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: a23b3b1084cf6776cee8583891ae3d879183d072
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
# <a name="add-sign-in-to-an-net-mvc-web-app"></a>Ajouter une connexion à une application Web MVC .NET
Avec le point de terminaison v2.0, vous pouvez rapidement ajouter une authentification à vos applications web, avec prise en charge des comptes Microsoft personnels et des comptes professionnels ou scolaires.  Dans les applications web ASP.NET, vous pouvez y parvenir en utilisant l’intergiciel OWIN de Microsoft inclus dans .NET Framework 4.5.

> [!NOTE]
> Les scénarios et les fonctionnalités Azure Active Directory ne sont pas tous pris en charge par le point de terminaison v2.0.  Pour déterminer si vous devez utiliser le point de terminaison v2.0, consultez les [limites de v2.0](active-directory-v2-limitations.md).
>
>

 Nous allons créer une application Web qui utilise OWIN pour connecter l’utilisateur, afficher des informations sur l’utilisateur et déconnecter l’utilisateur de l’application.

## <a name="download"></a>Download
Le code associé à ce didacticiel est stocké [sur GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet).  Pour suivre la procédure, vous pouvez [télécharger la structure de l’application au format .zip](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet/archive/skeleton.zip) ou la cloner :

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet.git```

L'application terminée est également fournie à la fin de ce didacticiel.

## <a name="register-an-app"></a>Inscription d’une application
Créez une application à l’adresse [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), ou suivez cette [procédure détaillée](active-directory-v2-app-registration.md).  Veillez à respecter les points suivants :

* copier l' **ID d'application** attribué à votre application, vous en aurez bientôt besoin ;
* Ajoutez la plate-forme **Web** pour votre application.
* entrer l’ **URI de redirection**approprié. L’URI de redirection indique à Azure AD où les réponses d’authentification doivent être dirigées. La valeur par défaut pour ce didacticiel est `https://localhost:44326/`.

## <a name="install--configure-owin-authentication"></a>Installer et configurer l’authentification OWIN
Ici, nous allons configurer l’intergiciel OWIN pour utiliser le protocole d’authentification OpenID Connect.  OWIN sera utilisé pour émettre des demandes de connexion et de déconnexion, gérer la session utilisateur et obtenir des informations concernant l’utilisateur, entre autres.

1. Pour commencer, ouvrez le fichier `web.config` dans la racine du projet, puis entrez les valeurs de configuration de votre application dans la section `<appSettings>`.

  * L’élément `ida:ClientId` est l’ **ID d’application** affecté à votre application dans le portail d’inscription.
  * L’élément `ida:RedirectUri` est l’ **URI de redirection** que vous avez saisi dans le portail.

2. Ajoutez ensuite les packages NuGet d'intergiciel (middleware) OWIN au projet à l'aide de la console du gestionnaire de package.

        ```
        PM> Install-Package Microsoft.Owin.Security.OpenIdConnect
        PM> Install-Package Microsoft.Owin.Security.Cookies
        PM> Install-Package Microsoft.Owin.Host.SystemWeb
        ```  

3. Ajoutez une classe de démarrage OWIN au projet nommé `Startup.cs` Cliquez avec le bouton droit sur le projet --> **Ajouter** --> **Nouvel élément** --> Recherchez « OWIN ».  L’intergiciel OWIN appelle la méthode `Configuration(...)` lorsque votre application démarre.
4. Modifiez la déclaration de classe en `public partial class Startup`. Nous avons déjà mis en œuvre une partie de cette classe pour vous, dans un autre fichier.  Dans la méthode `Configuration(...)`, appelez ConfigureAuth(...) pour configurer l’authentification de votre application web.  

        ```csharp
        [assembly: OwinStartup(typeof(Startup))]
        
        namespace TodoList_WebApp
        {
            public partial class Startup
            {
                public void Configuration(IAppBuilder app)
                {
                    ConfigureAuth(app);
                }
            }
        }
        ```

5. Ouvrez le fichier `App_Start\Startup.Auth.cs` et implémentez la méthode `ConfigureAuth(...)`.  Les paramètres que vous fournissez dans `OpenIdConnectAuthenticationOptions` serviront de coordonnées pour que votre application puisse communiquer avec Azure AD.  Vous devrez également configurer l’authentification des cookies ; l’intergiciel OpenID Connect utilise des cookies en coulisses.

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
                                             Authority = String.Format(CultureInfo.InvariantCulture, aadInstance, "common", "/v2.0"),
                                             RedirectUri = redirectUri,
                                             Scope = "openid email profile",
                                             ResponseType = "id_token",
                                             PostLogoutRedirectUri = redirectUri,
                                             TokenValidationParameters = new TokenValidationParameters
                                             {
                                                     ValidateIssuer = false,
                                             },
                                             Notifications = new OpenIdConnectAuthenticationNotifications
                                             {
                                                     AuthenticationFailed = OnAuthenticationFailed,
                                             }
                                     });
                     }
        ```

## <a name="send-authentication-requests"></a>Envoi de demandes d’authentification
Votre application est maintenant correctement configurée pour communiquer avec le point de terminaison v2.0 en utilisant le protocole d’authentification OpenID Connect.  OWIN a pris en charge tous les détails déplaisants de la création de messages d’authentification, de la validation des jetons d’Azure AD et de la gestion des sessions utilisateur.  Il ne reste qu’à offrir aux utilisateurs un moyen de se connecter et de se déconnecter.

- Vous pouvez utiliser des balises autorisées dans vos contrôleurs pour exiger que l’utilisateur se connecte avant d’accéder à une page donnée.  Ouvrez `Controllers\HomeController.cs` et ajoutez la balise `[Authorize]` au contrôleur About.
        
        ```csharp
        [Authorize]
        public ActionResult About()
        {
          ...
        ```

- Vous pouvez également utiliser OWIN pour émettre des demandes d’authentification provenant directement de votre code.  Ouvrez `Controllers\AccountController.cs`.  Dans les actions SignIn() et SignOut(), émettez les demandes de test OpenID Connect et de déconnexion, respectivement.

        ```csharp
        public void SignIn()
        {
            // Send an OpenID Connect sign-in request.
            if (!Request.IsAuthenticated)
            {
                HttpContext.GetOwinContext().Authentication.Challenge(new AuthenticationProperties { RedirectUri = "/" }, OpenIdConnectAuthenticationDefaults.AuthenticationType);
            }
        }
        
        // BUGBUG: Ending a session with the v2.0 endpoint is not yet supported.  Here, we just end the session with the web app.  
        public void SignOut()
        {
            // Send an OpenID Connect sign-out request.
            HttpContext.GetOwinContext().Authentication.SignOut(CookieAuthenticationDefaults.AuthenticationType);
            Response.Redirect("/");
        }
        ```

- À présent, ouvrez `Views\Shared\_LoginPartial.cshtml`.  Voici où vous allez afficher les liens de connexion et de déconnexion de votre application pour l’utilisateur et où vous allez afficher le nom de l’utilisateur.

        ```HTML
        @if (Request.IsAuthenticated)
        {
            <text>
                <ul class="nav navbar-nav navbar-right">
                    <li class="navbar-text">
        
                        @*The 'preferred_username' claim can be used for showing the user's primary way of identifying themselves.*@
        
                        Hello, @(System.Security.Claims.ClaimsPrincipal.Current.FindFirst("preferred_username").Value)!
                    </li>
                    <li>
                        @Html.ActionLink("Sign out", "SignOut", "Account")
                    </li>
                </ul>
            </text>
        }
        else
        {
            <ul class="nav navbar-nav navbar-right">
                <li>@Html.ActionLink("Sign in", "SignIn", "Account", routeValues: null, htmlAttributes: new { id = "loginLink" })</li>
            </ul>
        }
        ```

## <a name="display-user-information"></a>Afficher les informations utilisateur
Lors de l’authentification des utilisateurs avec OpenID Connect, le point de terminaison v2.0 retourne un jeton d’ID à l’application qui contient des revendications ou des assertions concernant l’utilisateur.  Vous pouvez utiliser ces revendications pour personnaliser votre application :

- Ouvrez le fichier `Controllers\HomeController.cs` .  Vous pouvez accéder aux revendications de l’utilisateur dans vos contrôleurs via l’objet principal de sécurité `ClaimsPrincipal.Current` .

        ```csharp
        [Authorize]
        public ActionResult About()
        {
            ViewBag.Name = ClaimsPrincipal.Current.FindFirst("name").Value;
        
            // The object ID claim will only be emitted for work or school accounts at this time.
            Claim oid = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier");
            ViewBag.ObjectId = oid == null ? string.Empty : oid.Value;
        
            // The 'preferred_username' claim can be used for showing the user's primary way of identifying themselves
            ViewBag.Username = ClaimsPrincipal.Current.FindFirst("preferred_username").Value;
        
            // The subject or nameidentifier claim can be used to uniquely identify the user
            ViewBag.Subject = ClaimsPrincipal.Current.FindFirst("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier").Value;
        
            return View();
        }
        ```

## <a name="run"></a>Exécuter
Enfin, générez et exécutez votre application.   Connectez-vous avec un compte Microsoft personnel ou un compte professionnel ou scolaire, et notez comment l’identité de l’utilisateur est indiquée dans la barre de navigation supérieure.  Vous disposez désormais d’une application web sécurisée à l’aide de protocoles standard et pouvant authentifier les utilisateurs avec leurs comptes personnels et professionnels/scolaires.

Pour référence, l’exemple terminé (sans vos valeurs de configuration) [est fourni ici au format .zip](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet/archive/complete.zip). Vous pouvez également le cloner à partir de GitHub :

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet.git```

## <a name="next-steps"></a>Étapes suivantes
Vous pouvez maintenant aborder des rubriques plus sophistiquées.  Par exemple :

[Sécuriser une API Web avec le point de terminaison v2.0 &gt;&gt;](active-directory-devquickstarts-webapi-dotnet.md)

Pour obtenir des ressources supplémentaires, consultez :

* [Guide du développeur 2.0 &gt;&gt;](active-directory-appmodel-v2-overview.md)
* [Balise StackOverflow "azure-active-directory" &gt;&gt;](http://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="get-security-updates-for-our-products"></a>Obtenir les mises à jour de sécurité de nos produits
Nous vous encourageons à activer les notifications d’incidents de sécurité en vous rendant sur [cette page](https://technet.microsoft.com/security/dd252948) et en vous abonnant aux alertes d’avis de sécurité.
