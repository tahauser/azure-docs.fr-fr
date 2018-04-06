---
title: Authentification, abonnement, réinitialisation de mot de passe ASP.NET, Azure Active Directory B2C
description: Comment créer une application web avec inscription/connexion, modification du profil et réinitialisation du mot de passe à l’aide d’Azure Active Directory B2C.
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
ms.openlocfilehash: f7b7cce84f8ff7be14120568e710432be639ce8e
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="create-an-aspnet-web-app-with-azure-active-directory-b2c-sign-up-sign-in-profile-edit-and-password-reset"></a>Créer une application web ASP.NET avec inscription, connexion, modification du profil et réinitialisation du mot de passe Azure Active Directory B2C

Ce didacticiel vous explique les procédures suivantes :

> [!div class="checklist"]
> * Ajouter des fonctionnalités d’identité Azure AD B2C à votre application web
> * Inscrire votre application web auprès de votre répertoire Azure AD B2C
> * Créer une stratégie d’inscription/de connexion d’utilisateur, de modification du profil et de réinitialisation du mot de passe pour votre application web

## <a name="prerequisites"></a>Prérequis


- Vous devez connecter votre locataire B2C à un compte Azure. Vous pouvez créer un compte Azure gratuit [ici](https://azure.microsoft.com/en-us/).
- Vous avez besoin de [Microsoft Visual Studio](https://www.visualstudio.com/) ou d’un programme similaire pour afficher et modifier l’exemple de code.

## <a name="create-an-azure-ad-b2c-directory"></a>Créer un répertoire Azure AD B2C

Avant de pouvoir utiliser Azure AD B2C, vous devez créer un répertoire ou un client. Un répertoire est un conteneur destiné à recevoir tous vos utilisateurs, applications, groupes et autres. Si vous n’en possédez pas déjà un, créez un répertoire B2C avant d’aller plus loin dans ce guide.

[!INCLUDE [active-directory-b2c-create-tenant](../../includes/active-directory-b2c-create-tenant.md)]

> [!NOTE]
> 
> Vous devez connecter le locataire B2C à votre abonnement Azure. Après avoir sélectionné **Créer**, sélectionnez l’option **Lier un locataire Azure AD B2C existant à mon abonnement Azure**, puis, dans le menu déroulant **Locataire Azure AD B2C**, sélectionnez le locataire que vous souhaitez associer.

## <a name="create-and-register-an-application"></a>Créer et inscrire une application

Vous devez maintenant créer et inscrire l’application dans votre répertoire B2C. Cela fournit certaines informations nécessaires à Azure AD B2C pour communiquer de manière sécurisée avec votre application. 

[!INCLUDE [active-directory-b2c-register-web-api](../../includes/active-directory-b2c-register-web-api.md)]

Lorsque vous avez terminé, vous aurez une API et une application native dans vos paramètres d’application.

## <a name="create-policies-on-your-b2c-tenant"></a>Créer des stratégies dans votre locataire B2C

Dans Azure AD B2C, chaque expérience utilisateur est définie par une [stratégie](active-directory-b2c-reference-policies.md). Cet exemple de code contient trois expériences liées à l’identité : **l’inscription et la connexion**, la **modification du profil** et la **réinitialisation du mot de passe**.  Vous devez créer une stratégie de chaque type, comme décrit dans [l’article de référence sur les stratégies](active-directory-b2c-reference-policies.md). Pour chaque stratégie, veillez à sélectionner l’attribut de nom d’affichage ou la revendication et à copier le nom de votre stratégie pour une utilisation ultérieure.

### <a name="add-your-identity-providers"></a>Ajouter vos fournisseurs d’identité

À partir de vos paramètres, sélectionnez **Fournisseurs d’identité** et choisissez Abonnement par nom d’utilisateur ou Abonnement par e-mail.

### <a name="create-a-sign-up-and-sign-in-policy"></a>Créer une stratégie d’inscription et de connexion

[!INCLUDE [active-directory-b2c-create-sign-in-sign-up-policy](../../includes/active-directory-b2c-create-sign-in-sign-up-policy.md)]

### <a name="create-a-profile-editing-policy"></a>Création d’une stratégie de modification de profil

[!INCLUDE [active-directory-b2c-create-profile-editing-policy](../../includes/active-directory-b2c-create-profile-editing-policy.md)]

### <a name="create-a-password-reset-policy"></a>Création d’une stratégie de réinitialisation du mot de passe

[!INCLUDE [active-directory-b2c-create-password-reset-policy](../../includes/active-directory-b2c-create-password-reset-policy.md)]

Une fois vos stratégies créées, vous pouvez générer votre application.

## <a name="download-the-sample-code"></a>Télécharger l’exemple de code

Le code associé à ce didacticiel est stocké sur [GitHub](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi). Vous pouvez cloner l’exemple en exécutant :

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

Une fois l’exemple de code téléchargé, ouvrez le fichier .sln Visual Studio pour commencer. Le fichier solution contient deux projets : `TaskWebApp` et `TaskService`. `TaskWebApp` est l’application web MVC avec laquelle l’utilisateur interagit. `TaskService` est l’API web du serveur principal de l’application qui stocke la liste de tâches de chaque utilisateur. Cet article traite uniquement de l’application `TaskWebApp`. Pour savoir comment générer `TaskService` à l’aide d’Azure AD B2C, consultez [notre didacticiel sur les API web .NET](active-directory-b2c-devquickstarts-api-dotnet.md).

## <a name="update-code-to-use-your-tenant-and-policies"></a>Mettre à jour le code pour utiliser votre locataire et vos stratégies

Notre exemple est configuré pour utiliser les stratégies et l’ID client du locataire de notre démonstration. Pour le connecter à votre propre locataire, vous devez ouvrir `web.config` dans le projet `TaskWebApp` et remplacer les valeurs suivantes :

* `ida:Tenant` par le nom de votre locataire
* `ida:ClientId` par votre ID d’application web
* `ida:ClientSecret` par votre clé secrète d’application web
* `ida:SignUpSignInPolicyId` par votre nom de stratégie d’inscription ou de connexion
* `ida:EditProfilePolicyId` par votre nom de stratégie « Modifier le profil »
* `ida:ResetPasswordPolicyId` par votre nom de stratégie « Réinitialiser le mot de passe »

## <a name="launch-the-app"></a>Lancer l’application
À partir de Visual Studio, lancez l’application. Accédez à l’onglet Liste de tâches et notez que l’URl est : https://login.microsoftonline.com/*VotreNomDeClient*/oauth2/v2.0/authorize?p=*NomDeVotreStratégieInscription*&client_id=*VotreIDdeClient*.....

Inscrivez-vous à l’application à l’aide de votre adresse e-mail ou nom d’utilisateur. Déconnectez-vous, puis reconnectez-vous et modifiez le profil ou réinitialisez le mot de passe. Déconnectez-vous et connectez-vous en tant qu’autre utilisateur. 

## <a name="add-social-idps"></a>Ajout d’IDP sociaux

Actuellement, l’application prend uniquement en charge l’inscription et la connexion d’utilisateur à l’aide de **comptes locaux** (les comptes stockés dans votre répertoire B2C qui utilisent un nom d’utilisateur et un mot de passe). Avec Azure AD B2C, vous pouvez ajouter la prise en charge d’autres **fournisseurs d’identité** sans modifier votre code.

Pour ajouter des fournisseurs d’identité sociaux à votre application, commencez par suivre les instructions détaillées figurant dans ces articles. Pour chaque fournisseur d’identité que vous souhaitez prendre en charge, vous devez inscrire une application dans ce système et obtenir un ID client.

* [Définir Facebook en tant qu’IDP](active-directory-b2c-setup-fb-app.md)
* [Définir Google en tant qu’IDP](active-directory-b2c-setup-goog-app.md)
* [Définir Amazon en tant qu’IDP](active-directory-b2c-setup-amzn-app.md)
* [Définir LinkedIn en tant qu’IDP](active-directory-b2c-setup-li-app.md)

Après avoir ajouté les fournisseurs d’identité à votre répertoire B2C, modifiez chacune de vos trois stratégies pour inclure ces nouveaux fournisseurs, comme décrit dans [l’article de référence sur les stratégies](active-directory-b2c-reference-policies.md). Après avoir enregistré vos stratégies, exécutez à nouveau l’application.  Les nouveaux fournisseurs d’identité doivent être ajoutés comme options d’inscription et de connexion dans chacune de vos expériences relatives à l’identité.

Vous pouvez tester vos stratégies et observer le résultat sur votre exemple d’application. Ajoutez ou supprimez des fournisseurs d’identité, manipulez des revendications d’application ou modifiez des attributs d’inscription. Faites des essais jusqu’à ce que vous compreniez la façon dont les stratégies, les requêtes d’authentification et la bibliothèque OWIN sont liées.

## <a name="sample-code-walkthrough"></a>Exemple de code de procédure pas à pas
Les sections suivantes vous montrent comment l’exemple de code d’application est configuré. Vous pouvez les utiliser comme guide pour le développement de vos futures applications.

### <a name="add-authentication-support"></a>Ajouter le support de l’authentification

Vous pouvez à présent configurer votre application pour utiliser Azure AD B2C. Votre application communique avec Azure AD B2C en envoyant des demandes d’authentification OpenID Connect. Les demandes déterminent l’expérience utilisateur que votre application souhaite exécuter en spécifiant la stratégie. Vous pouvez utiliser la bibliothèque OWIN de Microsoft pour envoyer des requêtes, exécuter les stratégies, gérer les sessions utilisateur, etc.

#### <a name="install-owin"></a>Installer OWIN

Pour commencer, ajoutez au projet les packages NuGet du middleware OWIN à l’aide de la console du gestionnaire de package Visual Studio.

```Console
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect
PM> Install-Package Microsoft.Owin.Security.Cookies
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

#### <a name="add-an-owin-startup-class"></a>Ajouter une classe de démarrage OWIN

Ajoutez une classe de démarrage OWIN à l’API appelée `Startup.cs`.  Cliquez avec le bouton droit sur le projet, sélectionnez **Ajouter** et **Nouvel élément**, puis recherchez OWIN. L’intergiciel OWIN appelle la méthode `Configuration(…)` lorsque votre application démarre.

Dans notre exemple, nous avons modifié la déclaration de classe sur `public partial class Startup` et implémenté l’autre partie de la classe dans `App_Start\Startup.Auth.cs`. À l’intérieur de la méthode `Configuration`, nous avons ajouté un appel vers `ConfigureAuth`, qui est défini dans `Startup.Auth.cs`. Après modification, `Startup.cs` ressemble à ceci :

```CSharp
// Startup.cs

public partial class Startup
{
    // The OWIN middleware will invoke this method when the app starts
    public void Configuration(IAppBuilder app)
    {
        // ConfigureAuth defined in other part of the class
        ConfigureAuth(app);
    }
}
```

#### <a name="configure-the-authentication-middleware"></a>Configurer le middleware d’authentification

Ouvrez le fichier `App_Start\Startup.Auth.cs` et implémentez la méthode `ConfigureAuth(...)`. Les paramètres que vous fournissez dans `OpenIdConnectAuthenticationOptions` servent de coordonnées pour que votre application puisse communiquer avec Azure AD B2C. Si vous ne spécifiez pas certains paramètres, la valeur par défaut est utilisée. Par exemple, nous ne spécifions pas `ResponseType` dans l’exemple. Par conséquent, la valeur par défaut `code id_token` est utilisée dans chaque requête sortante pour Azure Active Directory B2C.

Vous devez également configurer l’authentification des cookies. Le middleware OpenID Connect utilise des cookies pour gérer les sessions utilisateur, entre autres.

```CSharp
// App_Start\Startup.Auth.cs

public partial class Startup
{
    // Initialize variables ...

    // Configure the OWIN middleware
    public void ConfigureAuth(IAppBuilder app)
    {
        app.UseCookieAuthentication(new CookieAuthenticationOptions());
        app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

        app.UseOpenIdConnectAuthentication(
            new OpenIdConnectAuthenticationOptions
            {
                // Generate the metadata address using the tenant and policy information
                MetadataAddress = String.Format(AadInstance, Tenant, DefaultPolicy),

                // These are standard OpenID Connect parameters, with values pulled from web.config
                ClientId = ClientId,
                RedirectUri = RedirectUri,
                PostLogoutRedirectUri = RedirectUri,

                // Specify the callbacks for each type of notifications
                Notifications = new OpenIdConnectAuthenticationNotifications
                {
                    RedirectToIdentityProvider = OnRedirectToIdentityProvider,
                    AuthorizationCodeReceived = OnAuthorizationCodeReceived,
                    AuthenticationFailed = OnAuthenticationFailed,
                },

                // Specify the claims to validate
                TokenValidationParameters = new TokenValidationParameters
                {
                    NameClaimType = "name"
                },

                // Specify the scope by appending all of the scopes requested into one string (seperated by a blank space)
                Scope = $"{OpenIdConnectScopes.OpenId} {ReadTasksScope} {WriteTasksScope}"
            }
        );
    }

    // Implement the "Notification" methods...
}
```

Dans `OpenIdConnectAuthenticationOptions` ci-dessus, nous définissons un ensemble de fonctions de rappel pour les notifications spécifiques qui sont reçues par le middleware OpenID Connect. Ces comportements sont définis en utilisant un objet `OpenIdConnectAuthenticationNotifications` et sont stockés dans la variable `Notifications`. Dans notre exemple, nous définissons trois rappels différents en fonction de l’événement.

### <a name="using-different-policies"></a>Utilisation de différentes stratégies

La notification `RedirectToIdentityProvider` est déclenchée chaque fois qu’une demande est envoyée à Azure AD B2C. Dans la fonction de rappel `OnRedirectToIdentityProvider`, nous vérifions dans l’appel sortant si nous souhaitons utiliser une stratégie différente. Pour effectuer une réinitialisation de mot de passe ou modifier un profil, vous devez utiliser la stratégie correspondante, par exemple la stratégie de réinitialisation du mot de passe, au lieu de la stratégie d’inscription ou de connexion par défaut.

Dans notre exemple, lorsqu’un utilisateur souhaite réinitialiser le mot de passe ou modifier le profil, nous ajoutons la stratégie que nous préférons utiliser dans le contexte d’OWIN. Pour cela, procédez comme suit :

```CSharp
    // Let the middleware know you are trying to use the edit profile policy
    HttpContext.GetOwinContext().Set("Policy", EditProfilePolicyId);
```

Et vous pouvez implémenter la fonction de rappel `OnRedirectToIdentityProvider` de la manière suivante :

```CSharp
/*
*  On each call to Azure AD B2C, check if a policy (e.g. the profile edit or password reset policy) has been specified in the OWIN context.
*  If so, use that policy when making the call. Also, don't request a code (since it won't be needed).
*/
private Task OnRedirectToIdentityProvider(RedirectToIdentityProviderNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> notification)
{
    var policy = notification.OwinContext.Get<string>("Policy");

    if (!string.IsNullOrEmpty(policy) && !policy.Equals(DefaultPolicy))
    {
        notification.ProtocolMessage.Scope = OpenIdConnectScopes.OpenId;
        notification.ProtocolMessage.ResponseType = OpenIdConnectResponseTypes.IdToken;
        notification.ProtocolMessage.IssuerAddress = notification.ProtocolMessage.IssuerAddress.Replace(DefaultPolicy, policy);
    }

    return Task.FromResult(0);
}
```

### <a name="handling-authorization-codes"></a>Gestion des codes d’autorisation

La notification `AuthorizationCodeReceived` est déclenchée lors de la réception d’un code d’autorisation. Le middleware OpenID Connect ne prend pas en charge l’échange de codes pour les jetons d’accès. Vous pouvez échanger manuellement le code pour le jeton dans une fonction de rappel. Pour plus d’informations, consultez cette [documentation](active-directory-b2c-devquickstarts-web-api-dotnet.md).

### <a name="handling-errors"></a>Gestion des erreurs

La notification `AuthenticationFailed` est déclenchée lors de l’échec de l’authentification. Dans la méthode de rappel associée, vous pouvez gérer les erreurs comme vous le souhaitez. Vous devez toutefois ajouter une vérification du code d’erreur `AADB2C90118`. Lors de l’exécution de la stratégie d’inscription ou de connexion, l’utilisateur a la possibilité de sélectionner un lien **Vous avez oublié votre mot de passe ?**. Dans ce cas, Azure AD B2C envoie à votre application un code d’erreur indiquant qu’elle doit effectuer une demande à l’aide de la stratégie de réinitialisation de mot de passe à la place.

```CSharp
/*
* Catch any failures received by the authentication middleware and handle appropriately
*/
private Task OnAuthenticationFailed(AuthenticationFailedNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> notification)
{
    notification.HandleResponse();

    // Handle the error code that Azure AD B2C throws when trying to reset a password from the login page
    // because password reset is not supported by a "sign-up or sign-in policy"
    if (notification.ProtocolMessage.ErrorDescription != null && notification.ProtocolMessage.ErrorDescription.Contains("AADB2C90118"))
    {
        // If the user clicked the reset password link, redirect to the reset password route
        notification.Response.Redirect("/Account/ResetPassword");
    }
    else if (notification.Exception.Message == "access_denied")
    {
        notification.Response.Redirect("/");
    }
    else
    {
        notification.Response.Redirect("/Home/Error?message=" + notification.Exception.Message);
    }

    return Task.FromResult(0);
}
```

### <a name="send-authentication-requests-to-azure-ad"></a>Envoyer des demandes d’authentification à Azure AD

Votre application est maintenant correctement configurée pour communiquer avec Azure AD B2C en utilisant le protocole d’authentification OpenID Connect. OWIN gère tous les détails de la création de messages d’authentification, de la validation des jetons d’Azure AD B2C et de la gestion des sessions utilisateur. Il ne reste plus qu’à initier le flux de chaque utilisateur.

Lorsqu’un utilisateur sélectionne **S’inscrire/se connecter**, **Modifier le profil** ou **Réinitialiser le mot de passe** dans l’application web, l’action associée est appelée dans `Controllers\AccountController.cs` :

```CSharp
// Controllers\AccountController.cs

/*
*  Called when requesting to sign up or sign in
*/
public void SignUpSignIn()
{
    // Use the default policy to process the sign up / sign in flow
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge();
        return;
    }

    Response.Redirect("/");
}

/*
*  Called when requesting to edit a profile
*/
public void EditProfile()
{
    if (Request.IsAuthenticated)
    {
        // Let the middleware know you are trying to use the edit profile policy (see OnRedirectToIdentityProvider in Startup.Auth.cs)
        HttpContext.GetOwinContext().Set("Policy", Startup.EditProfilePolicyId);

        // Set the page to redirect to after editing the profile
        var authenticationProperties = new AuthenticationProperties { RedirectUri = "/" };
        HttpContext.GetOwinContext().Authentication.Challenge(authenticationProperties);

        return;
    }

    Response.Redirect("/");

}

/*
*  Called when requesting to reset a password
*/
public void ResetPassword()
{
    // Let the middleware know you are trying to use the reset password policy (see OnRedirectToIdentityProvider in Startup.Auth.cs)
    HttpContext.GetOwinContext().Set("Policy", Startup.ResetPasswordPolicyId);

    // Set the page to redirect to after changing passwords
    var authenticationProperties = new AuthenticationProperties { RedirectUri = "/" };
    HttpContext.GetOwinContext().Authentication.Challenge(authenticationProperties);

    return;
}
```

Vous pouvez également utiliser OWIN pour déconnecter l’utilisateur de l’application. Dans `Controllers\AccountController.cs`, voici ce que nous obtenons :

```CSharp
// Controllers\AccountController.cs

/*
*  Called when requesting to sign out
*/
public void SignOut()
{
    // To sign out the user, you should issue an OpenIDConnect sign out request.
    if (Request.IsAuthenticated)
    {
        IEnumerable<AuthenticationDescription> authTypes = HttpContext.GetOwinContext().Authentication.GetAuthenticationTypes();
        HttpContext.GetOwinContext().Authentication.SignOut(authTypes.Select(t => t.AuthenticationType).ToArray());
        Request.GetOwinContext().Authentication.GetAuthenticationTypes();
    }
}
```

En plus d’appeler explicitement une stratégie, vous pouvez utiliser une balise `[Authorize]` dans vos contrôleurs qui exécute une stratégie si l’utilisateur n’est pas connecté. Ouvrez `Controllers\HomeController.cs` et ajoutez la balise `[Authorize]` au contrôleur des revendications.  OWIN sélectionne la dernière stratégie configurée lorsque la balise `[Authorize]` est atteinte.

```CSharp
// Controllers\HomeController.cs

// You can use the Authorize decorator to execute a certain policy if the user is not already signed into the app.
[Authorize]
public ActionResult Claims()
{
  ...
```

### <a name="display-user-information"></a>Afficher les informations utilisateur

Lors de l’authentification des utilisateurs avec OpenID Connect, Azure AD B2C retourne un jeton d’ID à l’application qui contient des **revendications**. Il s’agit d’assertions concernant l’utilisateur. Vous pouvez utiliser des revendications pour personnaliser votre application.

Ouvrez le fichier `Controllers\HomeController.cs` . Vous pouvez accéder aux revendications de l’utilisateur dans vos contrôleurs par le biais de l’objet principal de sécurité `ClaimsPrincipal.Current` .

```CSharp
// Controllers\HomeController.cs

[Authorize]
public ActionResult Claims()
{
    Claim displayName = ClaimsPrincipal.Current.FindFirst(ClaimsPrincipal.Current.Identities.First().NameClaimType);
    ViewBag.DisplayName = displayName != null ? displayName.Value : string.Empty;
    return View();
}
```

Vous pouvez accéder à toutes les revendications que votre application reçoit de la même façon.  Une liste de toutes les revendications reçues par l’application est disponible sur la page **Revendications** .
