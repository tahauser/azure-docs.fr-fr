---
title: "Bien démarrer avec l’application web .NET Azure AD | Microsoft Docs"
description: "Créez une application web MVC .NET qui s’intègre avec Azure AD pour la connexion."
services: active-directory
documentationcenter: .net
author: dstrockis
manager: mtillman
editor: 
ms.assetid: e15a41a4-dc5d-4c90-b3fe-5dc33b9a1e96
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: 5025ebda6eb47c4155c098be4a5479d5c3814942
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="aspnet-web-app-sign-in-and-sign-out-with-azure-ad"></a>Connexion et déconnexion à une application web ASP.NET avec Azure AD
[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

En assurant une connexion et une déconnexion uniques avec seulement quelques lignes de code, Azure Active Directory (Azure AD) simplifie l’externalisation de la gestion des identités des applications web. Vous pouvez connecter et déconnecter les utilisateurs des applications web ASP.NET à l’aide de l’implémentation Microsoft de l’intergiciel OWIN (Open Web Interface for .NET). L’intergiciel OWIN communautaire est inclus dans .NET Framework 4.5. Cet article explique comment utiliser OWIN pour :

* connecter des utilisateurs à des applications web en utilisant Azure AD comme fournisseur d’identité ;
* afficher certaines informations utilisateur ;
* déconnecter des utilisateurs des applications.

## <a name="before-you-get-started"></a>Avant de commencer
* Téléchargez [la structure de l’application](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/skeleton.zip) ou [l’exemple terminé](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/complete.zip).
* Vous avez également besoin d’un client Azure AD dans lequel inscrire l’application. Si vous ne disposez pas encore d’un client Azure AD, [découvrez comment en obtenir un](active-directory-howto-tenant.md).

Lorsque vous êtes prêt, suivez les procédures des quatre sections qui suivent.

## <a name="step-1-register-the-new-app-with-azure-ad"></a>Étape 1 : Inscrire la nouvelle application auprès d’Azure AD
Pour configurer l’application pour l’authentification des utilisateurs, commencez par l’inscrire dans votre client en procédant de la façon suivante :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Dans la barre supérieure, cliquez sur le nom de votre compte. Dans la liste **Annuaire**, sélectionnez le client Active Directory dans lequel vous voulez inscrire l’application.
3. Dans le volet gauche, cliquez sur **Plus de services**, puis sélectionnez **Azure Active Directory**.
4. Cliquez sur **Inscriptions des applications**, puis sélectionnez **Ajouter**.
5. Suivez les invites et créez **une nouvelle application web et/ou une nouvelle API web**.
  * Le champ **Nom** décrit l’application aux utilisateurs.
  * Le champ **URL de connexion** indique l’URL de base de l’application. L’URL par défaut de la structure est https://localhost:44320/.
6. Une fois que vous avez terminé l’inscription, Azure AD affecte un ID d’application unique à l’application. Copiez la valeur de la page d’application pour l’utiliser dans les sections suivantes.
7. À partir de la page **Paramètres** -> **Propriétés** de votre application, mettez à jour l’URI ID d’application. Le champ **URI ID d’application** est un identificateur unique de l’application. La convention d’affectation de noms est `https://<tenant-domain>/<app-name>` (par exemple, `https://contoso.onmicrosoft.com/my-first-aad-app`).

## <a name="step-2-set-up-the-app-to-use-the-owin-authentication-pipeline"></a>Étape 2 : Configurer l’application pour utiliser le pipeline d’authentification OWIN
Au cours de cette étape, vous allez configurer l’intergiciel OWIN pour utiliser le protocole d’authentification OpenID Connect. Vous utilisez OWIN pour émettre des demandes de connexion et de déconnexion, gérer les sessions utilisateur, obtenir des informations utilisateur, et ainsi de suite.

1. Pour commencer, ajoutez au projet les packages NuGet de l’intergiciel OWIN à l’aide de la console du gestionnaire de package.

     ```
     PM> Install-Package Microsoft.Owin.Security.OpenIdConnect
     PM> Install-Package Microsoft.Owin.Security.Cookies
     PM> Install-Package Microsoft.Owin.Host.SystemWeb
     ```

2. Pour ajouter une classe de démarrage OWIN au projet nommé `Startup.cs`, cliquez avec le bouton droit sur le projet, sélectionnez **Ajouter** puis **Nouvel élément** et recherchez **OWIN**. L’intergiciel OWIN appelle la méthode **Configuration(...)** au lancement de l’application.
3. Remplacez la déclaration de classe par `public partial class Startup`. Nous avons déjà mis en œuvre une partie de cette classe pour vous dans un autre fichier. Dans la méthode **Configuration(...)**, appelez **ConfigureAuth(...)** pour configurer l’authentification de l’application.  

     ```C#
     public partial class Startup
     {
         public void Configuration(IAppBuilder app)
         {
             ConfigureAuth(app);
         }
     }
     ```

4. Ouvrez le fichier App_Start\Startup.Auth.cs, puis implémentez la méthode **ConfigureAuth(...)**. Les paramètres que vous fournissez dans *OpenIDConnectAuthenticationOptions* serviront de coordonnées pour que l’application puisse communiquer avec Azure AD. Vous devez également configurer l’authentification des cookies, car l’intergiciel OpenID Connect utilise des cookies en arrière-plan.

     ```C#
     public void ConfigureAuth(IAppBuilder app)
     {
         app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

         app.UseCookieAuthentication(new CookieAuthenticationOptions());

         app.UseOpenIdConnectAuthentication(
             new OpenIdConnectAuthenticationOptions
             {
                 ClientId = clientId,
                 Authority = authority,
                 PostLogoutRedirectUri = postLogoutRedirectUri,
                 Notifications = new OpenIdConnectAuthenticationNotifications
                    {
                        AuthenticationFailed = context =>
                        {
                            context.HandleResponse();
                            context.Response.Redirect("/Error?message=" + context.Exception.Message);
                            return Task.FromResult(0);
                        }
                    }
             });
     }
     ```

5. Ouvrez le fichier web.config à la racine du projet, puis entrez les valeurs de configuration dans la section `<appSettings>`.
  * `ida:ClientId` : le GUID que vous avez copié à partir du Portail Azure dans « Étape 1 : Inscrire la nouvelle application auprès d’Azure AD ».
  * `ida:Tenant` : le nom de votre client Azure AD (par exemple, contoso.onmicrosoft.com).
  * `ida:PostLogoutRedirectUri` : indique à Azure AD l’endroit vers lequel un utilisateur doit être redirigé après l’exécution d’une demande de déconnexion.

## <a name="step-3-use-owin-to-issue-sign-in-and-sign-out-requests-to-azure-ad"></a>Étape 3 : Utiliser OWIN pour émettre des demandes de connexion et de déconnexion dans Azure AD
L’application est maintenant correctement configurée pour communiquer avec Azure AD en utilisant le protocole d’authentification OpenID Connect. OWIN a géré tous les détails de la création de messages d’authentification, de la validation des jetons d’Azure AD et de la gestion des sessions utilisateur. Il ne reste qu’à offrir aux utilisateurs un moyen de se connecter et de se déconnecter.

1. Vous pouvez utiliser des balises autorisées dans vos contrôleurs afin d’exiger que les utilisateurs se connectent pour pouvoir accéder à une page donnée. Pour cela, ouvrez Controllers\HomeController.cs et ajoutez la balise `[Authorize]` à l’action About.

     ```C#
     [Authorize]
     public ActionResult About()
     {
       ...
     ```

2. Vous pouvez également utiliser OWIN pour émettre des demandes d’authentification provenant directement de votre code. Pour cela, ouvrez Controllers\AccountController.cs. Ensuite, dans les actions SignIn() et SignOut(), émettez les demandes de test OpenID Connect et de déconnexion.

     ```C#
     public void SignIn()
     {
         // Send an OpenID Connect sign-in request.
         if (!Request.IsAuthenticated)
         {
             HttpContext.GetOwinContext().Authentication.Challenge(new AuthenticationProperties { RedirectUri = "/" }, OpenIdConnectAuthenticationDefaults.AuthenticationType);
         }
     }
     public void SignOut()
     {
         // Send an OpenID Connect sign-out request.
         HttpContext.GetOwinContext().Authentication.SignOut(
              OpenIdConnectAuthenticationDefaults.AuthenticationType, CookieAuthenticationDefaults.AuthenticationType);
     }
     ```

3. Ouvrez Views\Shared\_LoginPartial.cshtml pour montrer les liens de connexion et de déconnexion de l’application à l’utilisateur et pour imprimer le nom de l’utilisateur dans un affichage.

    ```HTML
    @if (Request.IsAuthenticated)
    {
     <text>
         <ul class="nav navbar-nav navbar-right">
             <li class="navbar-text">
                 Hello, @User.Identity.Name!
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

## <a name="step-4-display-user-information"></a>Étape 4 : Afficher les informations utilisateur
Lorsqu’il authentifie les utilisateurs avec OpenID Connect, Azure AD renvoie un id_token à l’application qui contient des « revendications » ou des assertions concernant l’utilisateur. Vous pouvez utiliser ces revendications pour personnaliser l’application de la façon suivante :

1. Ouvrez le fichier Controllers\HomeController.cs. Vous pouvez accéder aux revendications de l’utilisateur dans vos contrôleurs via l’objet principal de sécurité `ClaimsPrincipal.Current` .

 ```C#
 public ActionResult About()
 {
     ViewBag.Name = ClaimsPrincipal.Current.FindFirst(ClaimTypes.Name).Value;
     ViewBag.ObjectId = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
     ViewBag.GivenName = ClaimsPrincipal.Current.FindFirst(ClaimTypes.GivenName).Value;
     ViewBag.Surname = ClaimsPrincipal.Current.FindFirst(ClaimTypes.Surname).Value;
     ViewBag.UPN = ClaimsPrincipal.Current.FindFirst(ClaimTypes.Upn).Value;

     return View();
 }
 ```

2. Générez et exécutez l'application. Si vous n’avez pas encore créé un nouvel utilisateur dans votre client avec un domaine onmicrosoft.com, il est temps de le faire. Voici comment procéder :

  a. Connectez-vous avec les informations d’identification de cet utilisateur et observez la façon dont l’identité de l’utilisateur se reflète dans la barre supérieure.

  b. Déconnectez-vous, puis reconnectez-vous sous le nom d’un autre utilisateur de votre client.

  c. Si vous vous sentez particulièrement ambitieux, enregistrez et exécutez une autre instance de cette application (avec son propre ID de client) et observez le fonctionnement de la connexion unique.

## <a name="next-steps"></a>Étapes suivantes
Pour référence, consultez [l’exemple terminé](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/complete.zip) (sans vos valeurs de configuration).

Vous pouvez maintenant aborder des rubriques plus avancées. Par exemple, essayez de [Sécuriser une API web avec Azure AD](active-directory-devquickstarts-webapi-dotnet.md).

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
