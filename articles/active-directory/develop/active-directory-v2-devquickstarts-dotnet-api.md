---
title: "Ajoutez la connexion à une API web MVC .NET à l’aide du point de terminaison Azure AD v2.0 | Microsoft Docs"
description: "Génération d’une API web MVC .NET qui accepte les jetons des comptes Microsoft personnels et des comptes professionnels ou scolaires."
services: active-directory
documentationcenter: .net
author: dstrockis
manager: mtillman
editor: 
ms.assetid: e77bc4e0-d0c9-4075-a3f6-769e2c810206
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/07/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: 65f25e2496065ca1aaba443a9d6b3e29239e0218
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
# <a name="secure-an-mvc-web-api"></a>Sécurisation d’une API Web MVC
Le point de terminaison v2.0 Azure Active Directory vous permet de protéger une API Web à l’aide des jetons d’accès [OAuth 2.0](active-directory-v2-protocols.md) , ce qui permet aux utilisateurs disposant d’un compte Microsoft personnel et de comptes professionnels ou scolaires d’accéder en toute sécurité à votre API Web.

> [!NOTE]
> Les scénarios et les fonctionnalités Azure Active Directory ne sont pas tous pris en charge par le point de terminaison v2.0.  Pour déterminer si vous devez utiliser le point de terminaison v2.0, consultez les [limites de v2.0](active-directory-v2-limitations.md).
>
>

Dans les API web ASP.NET, vous pouvez y parvenir en utilisant l’intergiciel OWIN de Microsoft inclus dans .NET Framework 4.5.  Ici, nous allons utiliser OWIN pour générer une API Web MVC « To Do List » (Liste de tâches) qui permet aux clients de créer et lire des tâches à partir de la liste de tâches d’un utilisateur.  L’API Web valide que les demandes entrantes contiennent un jeton d’accès valide et rejettent les demandes qui échouent à la validation sur un itinéraire protégé.  Cet exemple a été créé à l’aide de Visual Studio 2015.

## <a name="download"></a>Download
Le code associé à ce didacticiel est stocké [sur GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet).  Pour suivre la procédure, vous pouvez [télécharger la structure de l’application au format .zip](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/skeleton.zip) ou la cloner :

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet.git
```

L’application squelette comprend l’ensemble du code réutilisable associé à une API simple, mais n’intègre pas l’ensemble des éléments liés à l’identité. Si vous ne souhaitez pas suivre la procédure, vous pouvez cloner ou [télécharger l’exemple terminé](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/complete.zip).

```
git clone https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet.git
```

## <a name="register-an-app"></a>Inscription d’une application
Créez une application à l’adresse [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), ou suivez cette [procédure détaillée](active-directory-v2-app-registration.md).  Veillez à respecter les points suivants :

* copier l' **ID d'application** attribué à votre application, vous en aurez bientôt besoin ;

Cette solution Visual Studio contient également une valeur « TodoListClient », qui est une application simple WPF.  La valeur « TodoListClient » permet d'indiquer comment un utilisateur se connecte et comment un client peut émettre des requêtes à votre API Web.  Les valeurs TodoListClient et TodoListService sont, dans ce cas, représentées par la même application.  Pour configurer la valeur TodoListClient, vous devez également :

* ajouter la plateforme **Mobile** pour votre application ;

## <a name="install-owin"></a>Installer OWIN
Une fois l'application enregistrée, vous devez la configurer pour qu'elle puisse communiquer avec le point de terminaison v2.0 afin de valider les requêtes entrantes et les jetons.

* Pour commencer, ouvrez la solution et ajoutez les packages NuGet de l’intergiciel OWIN au projet TodoListService à l’aide de la console du gestionnaire de package.

```
PM> Install-Package Microsoft.Owin.Security.OAuth -ProjectName TodoListService
PM> Install-Package Microsoft.Owin.Security.Jwt -ProjectName TodoListService
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoListService
PM> Install-Package Microsoft.IdentityModel.Protocol.Extensions -ProjectName TodoListService
```

## <a name="configure-oauth-authentication"></a>Configurer l’authentification OAuth
* Ajoutez une classe de démarrage OWIN au projet TodoListService appelé `Startup.cs`.  Cliquez avec le bouton droit sur le projet --> **Ajouter** --> **Nouvel élément** --> Recherchez « OWIN ».  L’intergiciel OWIN appelle la méthode `Configuration(…)` lorsque votre application démarre.
* Modifiez la déclaration de classe en `public partial class Startup` .  Dans la méthode `Configuration(…)`, appelez ConfgureAuth(...) pour configurer l’authentification pour votre application web.

```csharp
public partial class Startup
{
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

* Ouvrez le fichier `App_Start\Startup.Auth.cs` et implémentez la méthode `ConfigureAuth(…)`, qui configure l’API web pour accepter les jetons du point de terminaison v2.0.

```csharp
public void ConfigureAuth(IAppBuilder app)
{
        var tvps = new TokenValidationParameters
        {
                // In this app, the TodoListClient and TodoListService
                // are represented using the same Application Id - we use
                // the Application Id to represent the audience, or the
                // intended recipient of tokens.

                ValidAudience = clientId,

                // In a real applicaiton, you might use issuer validation to
                // verify that the user's organization (if applicable) has
                // signed up for the app.  Here, we'll just turn it off.

                ValidateIssuer = false,
        };

        // Set up the OWIN pipeline to use OAuth 2.0 Bearer authentication.
        // The options provided here tell the middleware about the type of tokens
        // that will be recieved, which are JWTs for the v2.0 endpoint.

        // NOTE: The usual WindowsAzureActiveDirectoryBearerAuthenticaitonMiddleware uses a
        // metadata endpoint which is not supported by the v2.0 endpoint.  Instead, this
        // OpenIdConenctCachingSecurityTokenProvider can be used to fetch & use the OpenIdConnect
        // metadata document.

        app.UseOAuthBearerAuthentication(new OAuthBearerAuthenticationOptions
        {
                AccessTokenFormat = new Microsoft.Owin.Security.Jwt.JwtFormat(tvps, new OpenIdConnectCachingSecurityTokenProvider("https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration")),
        });
}
```

* Vous pouvez désormais utiliser les attributs `[Authorize]` pour protéger vos contrôleurs et vos actions avec l’authentification de porteur OAuth 2.0.  Décorez la classe `Controllers\TodoListController.cs` avec une balise authorize.  Cela force l’utilisateur à se connecter avant d’accéder à cette page.

```csharp
[Authorize]
public class TodoListController : ApiController
{
```

* Lorsqu’un appelant autorisé appelle correctement l’une des API `TodoListController` , l’action peut avoir besoin d’accéder aux informations sur l’appelant.  OWIN fournit l’accès aux revendications dans le jeton porteur via l’objet `ClaimsPrincipal` .  

```csharp
public IEnumerable<TodoItem> Get()
{
    // You can use the ClaimsPrincipal to access information about the
    // user making the call.  In this case, we use the 'sub' or
    // NameIdentifier claim to serve as a key for the tasks in the data store.

    Claim subject = ClaimsPrincipal.Current.FindFirst(ClaimTypes.NameIdentifier);

    return from todo in todoBag
           where todo.Owner == subject.Value
           select todo;
}
```

* Enfin, ouvrez le fichier `web.config` dans la racine du projet ToDoListService, puis entrez les valeurs de configuration dans la section `<appSettings>`.
  * `ida:Audience` est l’ **ID de l’application** que vous avez entré dans le portail.

## <a name="configure-the-client-app"></a>Configurer l’application cliente
Avant de pouvoir voir le service Todo List en action, vous devez configurer le client Todo List afin qu’il puisse obtenir des jetons du point de terminaison v2.0 et effectuer des appels au service.

* Dans le projet TodoListClient, ouvrez `App.config` et entrez les valeurs de votre configuration dans la section `<appSettings>`.
  * `ida:ClientId` est l’ID d’application que vous avez copié à partir du portail.

Enfin, nettoyez, générez et exécutez chaque projet.  Vous disposez maintenant d’une API web MVC .NET qui accepte les jetons des comptes Microsoft personnels et des comptes professionnels ou scolaires.  Connectez-vous au projet TodoListClient, puis appelez votre API web pour ajouter des tâches à la liste des tâches de l’utilisateur.

Pour référence, l’exemple terminé (sans vos valeurs de configuration) [est fourni ici au format .zip](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/complete.zip). Vous pouvez également le cloner à partir de GitHub :

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet.git```

## <a name="next-steps"></a>Étapes suivantes
Vous pouvez à présent passer à d’autres rubriques.  Par exemple :

[Appeler une API web à partir d’une application web &gt;&gt;](active-directory-v2-devquickstarts-webapp-webapi-dotnet.md)

Pour obtenir des ressources supplémentaires, consultez :

* [Guide du développeur 2.0 &gt;&gt;](active-directory-appmodel-v2-overview.md)
* [Balise StackOverflow "azure-active-directory" &gt;&gt;](http://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="get-security-updates-for-our-products"></a>Obtenir les mises à jour de sécurité de nos produits
Nous vous encourageons à activer les notifications d’incidents de sécurité en vous rendant sur [cette page](https://technet.microsoft.com/security/dd252948) et en vous abonnant aux alertes d’avis de sécurité.
