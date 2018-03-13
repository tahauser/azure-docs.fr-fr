---
title: "Bien démarrer avec l'API web Azure AD .NET | Microsoft Docs"
description: "Création d’une API web MVC .NET qui s’intègre à Azure AD pour l’authentification et l’autorisation."
services: active-directory
documentationcenter: .net
author: dstrockis
manager: mtillman
editor: 
ms.assetid: 67e74774-1748-43ea-8130-55275a18320f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: 4c4cf11b26402747ef58e4fa3fbbe2154876dfae
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
# <a name="azure-ad-net-web-api-getting-started"></a>Bien démarrer avec l'API web Azure AD .NET
[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Si vous créez une application qui fournit un accès aux ressources protégées, vous devez savoir comment éviter un accès non autorisé à ces ressources.
Azure Active Directory (Azure AD) facilite la protection d’une API web à l’aide de jetons d’accès du porteur OAuth Bearer 2.0 avec seulement quelques lignes de code.

Dans les applications web ASP.NET, vous pouvez y parvenir en utilisant l’implémentation Microsoft de l’intergiciel communautaire OWIN inclus dans .NET Framework 4.5. Ici, nous allons utiliser OWIN pour créer une API web « Liste des tâches » qui effectue les actions suivantes :

* Désigne les API qui sont protégées.
* Valide le fait que les appels d’API web contiennent un jeton d’accès valide.

Pour générer l’API To Do List, vous devez d’abord exécuter les actions suivantes :

1. inscrivez une application auprès d’Azure AD ;
2. configurez l’application pour utiliser le pipeline d’authentification OWIN ;
3. configurez une application cliente pour appeler l’API web.

Pour commencer, téléchargez [la structure de l’application](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/skeleton.zip) ou [l’exemple terminé](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/complete.zip). Chaque option est une solution Visual Studio 2013. Vous aurez également besoin d’un locataire Azure AD dans lequel inscrire votre application. Si ce n’est pas déjà fait, [découvrez comment en obtenir un](active-directory-howto-tenant.md).

## <a name="step-1-register-an-application-with-azure-ad"></a>Étape 1 : Inscrire une application auprès d’Azure AD
Pour sécuriser votre application, vous devez tout d’abord créer une application dans votre locataire et fournir quelques informations essentielles à Azure AD.

1. Connectez-vous au [Portail Azure](https://portal.azure.com).

2. Dans la barre supérieure, cliquez sur votre compte. Dans la liste **Répertoire**, choisissez le locataire Azure AD auprès duquel vous voulez inscrire votre application.

3. Dans le volet gauche, cliquez sur **Plus de services**, puis sélectionnez **Azure Active Directory**.

4. Cliquez sur **Inscriptions des applications**, puis sélectionnez **Ajouter**.

5. Suivez les invites et créez une **application web et/ou un API web**.
  * **Nom** décrit votre application pour les utilisateurs. Entrez **To Do List Service**.
  * **URI de redirection** est une combinaison de schémas et de chaînes qu’Azure AD utilise pour renvoyer les jetons demandés par l’application. Entrez `https://localhost:44321/` pour cette valeur.

6. À partir de la page **Paramètres** -> **Propriétés** de votre application, mettez à jour l’URI ID d’application. Entrez un identificateur propre au locataire. Par exemple, entrez : `https://contoso.onmicrosoft.com/TodoListService`.

7. Enregistrez la configuration. Laissez le portail ouvert, car vous devrez également bientôt inscrire votre application cliente.

## <a name="step-2-set-up-the-app-to-use-the-owin-authentication-pipeline"></a>Étape 2 : Configurer l’application pour utiliser le pipeline d’authentification OWIN
Pour valider des demandes entrantes et des jetons, vous devez configurer votre application pour communiquer avec Azure AD.

1. Pour commencer, ouvrez la solution et ajoutez les packages NuGet de l’intergiciel OWIN au projet TodoListService à l’aide de la console du Gestionnaire de package.

    ```
    PM> Install-Package Microsoft.Owin.Security.ActiveDirectory -ProjectName TodoListService
    PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoListService
    ```

2. Ajoutez une classe de démarrage OWIN au projet TodoListService appelé `Startup.cs`.  Cliquez avec le bouton droit sur le projet, sélectionnez **Ajouter** > **Nouvel élément**, puis recherchez **OWIN**. L’intergiciel OWIN appelle la méthode `Configuration(…)` lorsque votre application démarre.

3. Remplacez la déclaration de classe par `public partial class Startup`. Nous avons déjà mis en œuvre une partie de cette classe dans un autre fichier. Dans la méthode `Configuration(…)`, appelez `ConfgureAuth(…)` pour configurer l’authentification à votre application web.

    ```csharp
    public partial class Startup
    {
        public void Configuration(IAppBuilder app)
        {
            ConfigureAuth(app);
        }
    }
    ```

4. Ouvrez le fichier `App_Start\Startup.Auth.cs` et implémentez la méthode `ConfigureAuth(…)`. Les paramètres que vous fournissez dans `WindowsAzureActiveDirectoryBearerAuthenticationOptions` serviront de coordonnées pour que votre application puisse communiquer avec Azure AD.

    ```csharp
    public void ConfigureAuth(IAppBuilder app)
    {
        app.UseWindowsAzureActiveDirectoryBearerAuthentication(
            new WindowsAzureActiveDirectoryBearerAuthenticationOptions
            {
                Audience = ConfigurationManager.AppSettings["ida:Audience"],
                Tenant = ConfigurationManager.AppSettings["ida:Tenant"]
            });
    }
    ```

5. Vous pouvez désormais utiliser les attributs `[Authorize]` pour protéger vos contrôleurs et vos actions avec l’authentification de porteur JWT (JSON Web Token). Décorez la classe `Controllers\TodoListController.cs` avec une balise authorize. Cela force l’utilisateur à se connecter avant d’accéder à cette page.

    ```csharp
    [Authorize]
    public class TodoListController : ApiController
    {
    ```

    Lorsqu’un appelant autorisé appelle correctement l’une des API `TodoListController` , l’action peut avoir besoin d’accéder aux informations sur l’appelant. OWIN fournit l’accès aux revendications dans le jeton porteur via l’objet `ClaimsPrincpal` .  

6. Une exigence courante pour les API web consiste à valider les « étendues » présentes dans le jeton. Cela garantit que l’utilisateur a donné son consentement pour les autorisations nécessaires pour accéder au To Do List Service.

    ```csharp
    public IEnumerable<TodoItem> Get()
    {
        // user_impersonation is the default permission exposed by applications in Azure AD
        if (ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/scope").Value != "user_impersonation")
        {
            throw new HttpResponseException(new HttpResponseMessage {
              StatusCode = HttpStatusCode.Unauthorized,
              ReasonPhrase = "The Scope claim does not contain 'user_impersonation' or scope claim not found"
            });
        }
        ...
    }
    ```

7. Ouvrez le fichier `web.config` dans la racine du projet ToDoListService, puis entrez les valeurs de configuration dans la section `<appSettings>`.
  * `ida:Tenant` est le nom de votre locataire Azure AD, par exemple, contoso.onmicrosoft.com.
  * `ida:Audience` est l’URI ID d’application que vous avez entré dans le portail Azure.

## <a name="step-3-configure-a-client-application-and-run-the-service"></a>Étape 3 : Configurer une application cliente et exécuter le service
Avant de pouvoir voir le service Todo List en action, vous devez configurer le client Todo List afin qu’il puisse obtenir des jetons d’Azure AD et appeler le service.

1. Revenez au [portail Azure](https://portal.azure.com).

2. Créez une application dans votre client Azure AD, puis sélectionnez **Application cliente native** dans l’invite qui en résulte.
  * **Nom** décrit votre application pour les utilisateurs.
  * Pour **l’URI de redirection**, entrez `http://TodoListClient/`.

3. Une fois l’inscription terminée, Azure AD affecte un ID d’application unique à votre application. Copiez cette valeur à partir de la page de l’application, car vous en aurez besoin dans les étapes suivantes.

4. Dans la page **Paramètres**, sélectionnez **Autorisations requises**, puis **Ajouter**. Recherchez et sélectionnez le service To Do List, ajoutez l’autorisation **d’accès à TodoListService** sous **Autorisations déléguées**, puis cliquez sur **Terminé**.

5. Dans Visual Studio, ouvrez `App.config` dans le projet TodoListClient, puis entrez les valeurs de votre configuration dans la section `<appSettings>`.

  * `ida:Tenant` est le nom de votre locataire Azure AD, par exemple, contoso.onmicrosoft.com.
  * `ida:ClientId` est l’ID d’application que vous avez copié à partir du portail Azure.
  * `todo:TodoListResourceId` est l’URI ID d’application de l’application To Do List Service que vous avez entré dans le portail Azure.

## <a name="next-steps"></a>Étapes suivantes
Enfin, nettoyez, générez et exécutez chaque projet. Si vous ne l’avez pas encore fait, il est temps de créer un nouvel utilisateur dans votre client, avec un domaine *.onmicrosoft.com. Connectez-vous au locataire To Do List avec cet utilisateur et ajoutez quelques tâches à la liste des tâches de cet utilisateur.

Pour référence, l’exemple terminé (sans vos valeurs de configuration) est disponible dans [GitHub](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/complete.zip). Vous pouvez à présent aborder d’autres scénarios d’identité.

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
