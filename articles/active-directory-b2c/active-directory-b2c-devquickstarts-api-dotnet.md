---
title: Sécuriser une API web - ASP.NET - Azure Active Directory B2C | Microsoft Docs
description: Comment créer une API web .NET à l’aide d’Azure Active Directory B2C et la sécuriser à l’aide des jetons d’accès OAuth 2.0 pour l’authentification,
services: active-directory-b2c
documentationcenter: .net
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 01/14/2018
ms.author: davidmu
ms.openlocfilehash: c64ca3546c5c2d21b00b379c297c9265b264cb9a
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-build-a-net-web-api"></a>Azure Active Directory B2C : générer une API web .NET

Avec Azure Active Directory (Azure AD) B2C, vous pouvez sécuriser une API web à l’aide de jetons d’accès OAuth 2.0, ce qui permet aux applications clientes de s’authentifier auprès de l’API. Cet article vous indique comment créer une API .NET MVC « liste de tâches » permettant aux utilisateurs de votre application cliente d’exécuter des actions de création, lecture, mise à jour et suppression (CRUD) sur les tâches. L’API web est sécurisée à l’aide d’Azure AD B2C et autorise uniquement les utilisateurs authentifiés à gérer leur liste de tâches.

## <a name="create-an-azure-ad-b2c-directory"></a>Créer un répertoire Azure AD B2C

Avant de pouvoir utiliser Azure AD B2C, vous devez créer un répertoire ou un client. Un répertoire est un conteneur destiné à recevoir tous vos utilisateurs, applications, groupes, etc. Si vous n’en possédez pas déjà un, [créez un répertoire B2C](active-directory-b2c-get-started.md) avant d’aller plus loin dans ce guide.

> [!NOTE]
> L’application cliente et l’API web doivent utiliser le même répertoire Azure AD B2C.
>

## <a name="create-a-web-api"></a>Créer une API Web

Vous devez maintenant créer dans votre répertoire B2C une application API web. fournissant à Azure AD certaines informations nécessaires pour communiquer de manière sécurisée avec votre application. Pour créer une application, suivez [ces instructions](active-directory-b2c-app-registration.md). Veillez à effectuer les opérations suivantes :

* Incluez une **application web** ou une **API web** dans l’application.
* Utilisez **l’URI de redirection** `https://localhost:44332/` pour l’application web. Il s’agit de l’emplacement par défaut du client d’application web pour cet exemple de code.
* Copiez l’ **ID d’application** affecté à votre application. Vous en aurez besoin ultérieurement.
* Entrez un identificateur d’application dans **l’URI d’ID d’application**. Copiez l’intégralité de **l’URI ID d’application**. Vous en aurez besoin ultérieurement.
* Ajoutez des autorisations via le menu **Étendues publiées**.

## <a name="create-your-policies"></a>Création de vos stratégies

Dans Azure AD B2C, chaque expérience utilisateur est définie par une [stratégie](active-directory-b2c-reference-policies.md). Vous devez créer une stratégie pour communiquer avec Azure AD B2C. Nous recommandons l’utilisation d’une stratégie combinée d’inscription/de connexion, comme décrit dans [l’article de référence sur les stratégies](active-directory-b2c-reference-policies.md). Lorsque vous créez la stratégie, prenez soin de :

* Choisir un **Nom d’affichage** et d’autres attributs d’inscription dans votre stratégie.
* Choisir les revendications **Nom d’affichage** et **ID d’objet** comme revendications d’application pour chaque stratégie. Vous pouvez aussi choisir d'autres revendications.
* Copiez le **nom** de chaque stratégie après sa création. Le nom de stratégie vous sera demandé ultérieurement.

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Une fois votre stratégie créée, vous pouvez générer votre application.

## <a name="download-the-code"></a>Téléchargement du code

Le code associé à ce didacticiel est stocké sur [GitHub](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi). Vous pouvez cloner l’exemple en exécutant :

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

Une fois l’exemple de code téléchargé, ouvrez le fichier .sln Visual Studio pour commencer. Le fichier solution contient deux projets : `TaskWebApp` et `TaskService`. `TaskWebApp` est une application web MVC avec laquelle l’utilisateur interagit. `TaskService` est l’API web du serveur principal de l’application qui stocke la liste de tâches de chaque utilisateur. Cet article traite uniquement l’application `TaskService`. Pour savoir comment générer `TaskWebApp` à l’aide d’Azure AD B2C, consultez [notre didacticiel sur les applications web .NET](active-directory-b2c-devquickstarts-web-dotnet-susi.md).

### <a name="update-the-azure-ad-b2c-configuration"></a>Mettre à jour la configuration Azure AD B2C

Notre exemple est configuré pour utiliser les stratégies et l’ID client du client de notre démonstration. Si vous souhaitez utiliser votre propre client, vous devez effectuer les opérations suivantes :

1. Ouvrez `web.config` dans le projet `TaskService` et remplacez les valeurs de
    * `ida:Tenant` avec le nom de votre client
    * `ida:ClientId` avec votre ID d’application d’API web
    * `ida:SignUpSignInPolicyId` avec votre nom de stratégie d’inscription ou de connexion

2. Ouvrez `web.config` dans le projet `TaskWebApp` et remplacez les valeurs de
    * `ida:Tenant` par le nom de votre locataire
    * `ida:ClientId` par votre ID d’application web
    * `ida:ClientSecret` par votre clé secrète d’application web
    * `ida:SignUpSignInPolicyId` par votre nom de stratégie d’inscription ou de connexion
    * `ida:EditProfilePolicyId` par votre nom de stratégie « Modifier le profil »
    * `ida:ResetPasswordPolicyId` par votre nom de stratégie « Réinitialiser le mot de passe »
    * `api:ApiIdentifier` par « URI ID d’application »


## <a name="secure-the-api"></a>Sécuriser l’API

Lorsque l’un de vos clients appelle votre API, vous pouvez la sécuriser (par exemple, `TaskService`) à l’aide de jetons du porteur OAuth 2.0. Cela garantit que chaque demande à votre API n’est valide que si la demande a un jeton de support. Votre API peut accepter et valider les jetons du porteur à l’aide de la bibliothèque OWIN (Open Web Interface for .NET) de Microsoft.

### <a name="install-owin"></a>Installer OWIN

Commencez par installer le pipeline d’authentification OWIN OAuth à l’aide de la console du gestionnaire de package Visual Studio.

```Console
PM> Install-Package Microsoft.Owin.Security.OAuth -ProjectName TaskService
PM> Install-Package Microsoft.Owin.Security.Jwt -ProjectName TaskService
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TaskService
```

Cette opération installe l’intergiciel OWIN qui accepte et valide les jetons du porteur.

### <a name="add-an-owin-startup-class"></a>Ajouter une classe de démarrage OWIN

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

### <a name="configure-oauth-20-authentication"></a>Configurer l’authentification OAuth 2.0

Ouvrez le fichier `App_Start\Startup.Auth.cs` et implémentez la méthode `ConfigureAuth(...)`. Par exemple, il peut ressembler à ceci :

```CSharp
// App_Start\Startup.Auth.cs

 public partial class Startup
    {
        // These values are pulled from web.config
        public static string AadInstance = ConfigurationManager.AppSettings["ida:AadInstance"];
        public static string Tenant = ConfigurationManager.AppSettings["ida:Tenant"];
        public static string ClientId = ConfigurationManager.AppSettings["ida:ClientId"];
        public static string SignUpSignInPolicy = ConfigurationManager.AppSettings["ida:SignUpSignInPolicyId"];
        public static string DefaultPolicy = SignUpSignInPolicy;

        /*
         * Configure the authorization OWIN middleware.
         */
        public void ConfigureAuth(IAppBuilder app)
        {
            TokenValidationParameters tvps = new TokenValidationParameters
            {
                // Accept only those tokens where the audience of the token is equal to the client ID of this app
                ValidAudience = ClientId,
                AuthenticationType = Startup.DefaultPolicy
            };

            app.UseOAuthBearerAuthentication(new OAuthBearerAuthenticationOptions
            {
                // This SecurityTokenProvider fetches the Azure AD B2C metadata & signing keys from the OpenIDConnect metadata endpoint
                AccessTokenFormat = new JwtFormat(tvps, new OpenIdConnectCachingSecurityTokenProvider(String.Format(AadInstance, Tenant, DefaultPolicy)))
            });
        }
    }
```

### <a name="secure-the-task-controller"></a>Sécuriser le contrôleur de tâche

Une fois que l’application est configurée pour utiliser l’authentification OAuth 2.0, vous pouvez sécuriser votre API web en ajoutant une balise `[Authorize]` au contrôleur de tâches. Comme il s’agit du contrôleur sur lequel toutes les manipulations de liste des tâches ont lieu, vous devez sécuriser l’ensemble du contrôleur au niveau de la classe. Vous pouvez également ajouter la balise `[Authorize]` à des actions individuelles afin d’obtenir un contrôle plus précis.

```CSharp
// Controllers\TasksController.cs

[Authorize]
public class TasksController : ApiController
{
    ...
}
```

### <a name="get-user-information-from-the-token"></a>Obtenir des informations utilisateur à partir du jeton

Le `TasksController` stocke des tâches dans une base de données où chaque tâche est associée à un utilisateur « propriétaire » de la tâche. Le propriétaire est identifié par **l’ID objet** de l’utilisateur. (c’est pourquoi vous devez ajouter l’ID d’objet en tant que revendication d’application dans toutes vous stratégies).

```CSharp
// Controllers\TasksController.cs

public IEnumerable<Models.Task> Get()
{
    string owner = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
    IEnumerable<Models.Task> userTasks = db.Tasks.Where(t => t.owner == owner);
    return userTasks;
}
```

### <a name="validate-the-permissions-in-the-token"></a>Valider les autorisations dans le jeton

Une exigence courante pour les API web consiste à valider les « étendues » présentes dans le jeton. Cela garantit que l’utilisateur a donné son consentement pour les autorisations nécessaires pour accéder au To Do List Service.

```CSharp
public IEnumerable<Models.Task> Get()
{
    if (ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/scope").Value != "read")
    {
        throw new HttpResponseException(new HttpResponseMessage {
            StatusCode = HttpStatusCode.Unauthorized,
            ReasonPhrase = "The Scope claim does not contain 'read' or scope claim not found"
        });
    }
    ...
}
```

## <a name="run-the-sample-app"></a>Exécution de l'exemple d'application

Pour finir, générez et exécutez `TaskWebApp` et `TaskService`. Créez des tâches sur la liste des tâches de l’utilisateur et notez la façon dont elles restent dans l’API, même une fois que vous avez arrêté et redémarré le client.

## <a name="edit-your-policies"></a>Modifier vos stratégies

Une fois que vous avez sécurisé une API avec Azure AD B2C, vous pouvez tester votre stratégie de connexion/d’inscription et afficher le résultat (ou l’absence de résultat) sur l’API. Vous pouvez manipuler les revendications d’application dans les stratégies et modifier les informations utilisateur qui sont disponibles dans l’API web. Toutes les revendications que vous ajoutez seront accessibles par votre API web .NET MVC dans l’objet `ClaimsPrincipal` , comme décrit plus haut dans cet article.
