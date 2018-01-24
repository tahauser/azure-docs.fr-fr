---
title: "Bien démarrer avec la plateforme universelle Azure AD Windows (UWP/XAML) | Microsoft Docs"
description: "Créez des applications Windows Store qui s’intègrent avec Azure AD pour la connexion et appellent des API protégées par Azure AD en utilisant OAuth."
services: active-directory
documentationcenter: windows
author: jmprieur
manager: mtillman
editor: 
ms.assetid: 3b96a6d1-270b-4ac1-b9b5-58070c896a68
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 11/30/2017
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 2282a59c9dd5d5d76a5b3e19f602e9d3dcc0b4ef
ms.sourcegitcommit: 234c397676d8d7ba3b5ab9fe4cb6724b60cb7d25
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/20/2017
---
# <a name="azure-ad-windows-universal-platform-uwpxaml-getting-started"></a>Bien démarrer avec la plateforme universelle Azure AD Windows (UWP/XAML)
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

> [!NOTE]
> Les projets du Windows Store 8.1 et des versions antérieures ne sont pas pris en charge dans Visual Studio 2017.  Pour en savoir plus, consultez [Plateforme cible et compatibilité dans Visual Studio 2017](https://www.visualstudio.com/en-us/productinfo/vs2017-compatibility-vs).

Si vous développez des applications pour Windows Store, Azure Active Directory (Azure AD) facilite l’authentification de vos utilisateurs avec leurs comptes Active Directory. Grâce à l’intégration avec Azure AD, une application peut utiliser en toute sécurité une API web qui est protégée par Azure AD, telle que les API Office 365 ou l’API Azure.

Pour les applications de bureau Windows Store qui doivent accéder à des ressources protégées, Azure AD fournit la bibliothèque d’authentification Active Directory (ADAL). Cette bibliothèque a pour seule fonction de simplifier l’obtention des jetons d’accès pour l’application. Pour illustrer cette simplicité, cet article montre comment créer une application Windows Store DirectorySearcher effectuant les actions suivantes :

* obtention de jetons d’accès pour appeler l’API Graph Azure AD à l’aide du [protocole d’authentification OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx) ;
* recherche, dans un répertoire, d’utilisateurs correspondant à un nom d’utilisateur principal (UPN) donné ;
* déconnexion des utilisateurs.

## <a name="before-you-get-started"></a>Avant de commencer
* Téléchargez la [structure du projet](https://github.com/AzureADQuickStarts/NativeClient-WindowsStore/archive/skeleton.zip) ou l’[exemple terminé](https://github.com/AzureADQuickStarts/NativeClient-WindowsStore/archive/complete.zip). Chaque téléchargement est une solution Visual Studio 2015.
* Vous avez également besoin d’un locataire Azure AD dans lequel créer les utilisateurs et inscrire l’application. Si vous ne disposez pas encore d’un client, [découvrez comment en obtenir un](active-directory-howto-tenant.md).

Lorsque vous êtes prêt, suivez les procédures des trois sections qui suivent.

## <a name="step-1-register-the-directorysearcher-app"></a>Étape 1 : Inscrire l’application DirectorySearcher
Pour autoriser l’application à obtenir des jetons, vous devez tout d’abord l’inscrire dans votre locataire Azure AD et lui accorder l’autorisation d’accéder à l’API Graph Azure AD. Voici comment procéder :

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Dans la barre supérieure, cliquez sur votre compte. Puis, dans la liste **Annuaire**, sélectionnez le client Active Directory dans lequel vous voulez inscrire l’application.
3. Dans le volet gauche, cliquez sur **Plus de services**, puis sélectionnez **Azure Active Directory**.
4. Cliquez sur **Inscriptions des applications**, puis sélectionnez **Ajouter**.
5. Suivez les invites à l’écran pour créer une **application cliente native**.
  * Le champ **Nom** décrit l’application aux utilisateurs.
  * L’**URI de redirection** est une combinaison de schémas et de chaînes qu’Azure AD utilise pour renvoyer des réponses concernant les jetons. Entrez une valeur d’espace réservé pour l’instant (par exemple, **http://DirectorySearcher**). Vous remplacerez cette valeur ultérieurement.
6. Une fois que vous avez terminé l’inscription, Azure AD affecte un ID d’application unique à l’application. Copiez la valeur dans l’onglet **Application**, car vous en aurez besoin ultérieurement.
7. Dans la page **Paramètres**, sélectionnez **Autorisations requises**, puis **Ajouter**.
8. Pour l’application **Azure Active Directory**, sélectionnez **Microsoft Graph** comme API.
9. Sous **Autorisations déléguées**, ajoutez l’autorisation **Accéder au répertoire en tant qu’utilisateur actuellement connecté**. Cela permet à l’application d’interroger l’API Graph pour les utilisateurs.

## <a name="step-2-install-and-configure-adal"></a>Étape 2 : Installer et configurer la bibliothèque ADAL
Maintenant que vous disposez d’une application dans Azure AD, vous pouvez installer la bibliothèque ADAL et écrire votre code lié à l’identité. Pour que la bibliothèque ADAL puisse communiquer avec Azure AD, fournissez-lui des informations sur l’inscription des applications.

1. Ajoutez ADAL au projet DirectorySearcher à l’aide de la console du gestionnaire de package.

    ```
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

2. Dans le projet DirectorySearcher, ouvrez MainPage.xaml.cs.
3. Remplacez les valeurs dans la région **Valeurs de configuration** par les valeurs que vous avez entrées dans le portail Azure. Votre code se réfère à ces valeurs chaque fois qu’il utilise la bibliothèque ADAL.
  * *tenant* est le domaine de votre client Azure AD (par exemple, contoso.onmicrosoft.com).
  * Le *clientId* est l’ID client de l’application, que vous avez copié à partir du portail.
4. Vous devez maintenant découvrir l’URI de rappel pour votre application Windows Store. Définissez un point d’arrêt sur cette ligne dans la méthode `MainPage` :
    ```
    redirectURI = Windows.Security.Authentication.Web.WebAuthenticationBroker.GetCurrentApplicationCallbackUri();
    ```
5. Générez la solution en vous assurant que toutes les références de package sont restaurées. Si des packages sont manquants, ouvrez le Gestionnaire de package NuGet et restaurez-les.
6. Exécutez l’application et copiez la valeur de `redirectUri` lorsque le point d’arrêt est atteint. La valeur doit ressembler à ceci :

    ```
    ms-app://s-1-15-2-1352796503-54529114-405753024-3540103335-3203256200-511895534-1429095407/
    ```

7. De nouveau sous l’onglet **Paramètres** de l’application dans le portail Azure, ajoutez une **URI de redirection** avec la valeur précédente.  

## <a name="step-3-use-adal-to-get-tokens-from-azure-ad"></a>Étape 3 : Utiliser la bibliothèque ADAL pour obtenir des jetons à partir d’Azure AD
Le principe de base de la bibliothèque ADAL consiste simplement à appeler `authContext.AcquireToken(…)` chaque fois que l’application a besoin d’un jeton d’accès, et la bibliothèque ADAL s’occupe du reste.  

1. Initialisez le `AuthenticationContext` de l’application, qui est la classe principale de la bibliothèque ADAL. Cette action fournit à la bibliothèque ADAL les coordonnées dont elle a besoin pour communiquer avec Azure AD et lui indique comment mettre en cache des jetons.

    ```C#
    public MainPage()
    {
        ...

        authContext = new AuthenticationContext(authority);
    }
    ```

2. Recherchez la méthode `Search(...)`, qui est appelée lorsque les utilisateurs cliquent sur le bouton **Rechercher** dans l’interface utilisateur de l’application. Cette méthode effectue une demande GET auprès de l’API Graph Azure AD pour l’interroger à propos d’utilisateurs dont l’UPN commence par le terme de recherche donné. Pour interroger l’API Graph, incluez un jeton d’accès dans l’en-tête **Autorisation** de la demande. C’est là où la bibliothèque ADAL entre en jeu.

    ```C#
    private async void Search(object sender, RoutedEventArgs e)
    {
        ...
        AuthenticationResult result = null;
        try
        {
            result = await authContext.AcquireTokenAsync(graphResourceId, clientId, redirectURI, new PlatformParameters(PromptBehavior.Auto, false));
        }
        catch (AdalException ex)
        {
            if (ex.ErrorCode != "authentication_canceled")
            {
                ShowAuthError(string.Format("If the error continues, please contact your administrator.\n\nError: {0}\n\nError Description:\n\n{1}", ex.ErrorCode, ex.Message));
            }
            return;
        }
        ...
    }
    ```
    Lorsque l’application demande un jeton en appelant `AcquireTokenAsync(...)`, la bibliothèque ADAL tente de renvoyer un jeton sans demander à l’utilisateur ses informations d’identification. Si la bibliothèque ADAL détermine que l’utilisateur doit se connecter pour obtenir un jeton, elle affiche une boîte de dialogue de connexion, récupère les informations d’identification de l’utilisateur et renvoie un jeton lorsque l’authentification réussit. Si la bibliothèque ADAL ne peut pas renvoyer un jeton pour une raison quelconque, l’état *AuthenticationResult* est une erreur.
3. Il est à présent temps d’utiliser le jeton d’accès que vous venez d’acquérir. Également dans la méthode `Search(...)`, joignez le jeton à la demande GET de l’API Graph, dans l’en-tête **Autorisation** :

    ```C#
    // Add the access token to the Authorization header of the call to the Graph API, and call the Graph API.
    httpClient.DefaultRequestHeaders.Authorization = new HttpCredentialsHeaderValue("Bearer", result.AccessToken);

    ```
4. Vous pouvez utiliser l’objet `AuthenticationResult` pour afficher des informations concernant l’utilisateur dans l’application, par exemple l’ID de l’utilisateur :

    ```C#
    // Update the page UI to represent the signed-in user
    ActiveUser.Text = result.UserInfo.DisplayableId;
    ```
5. Vous pouvez également utiliser la bibliothèque ADAL pour déconnecter des utilisateurs de l’application. Lorsque l’utilisateur clique sur le bouton **Déconnexion**, vérifiez que l’appel suivant vers `AcquireTokenAsync(...)` affiche une fenêtre de connexion. Avec la bibliothèque ADAL, cette action aussi simple que d’effacer le cache de jeton :

    ```C#
    private void SignOut()
    {
        // Clear session state from the token cache.
        authContext.TokenCache.Clear();

        ...
    }
    ```

## <a name="whats-next"></a>Étapes suivantes
Vous disposez maintenant d’une application Windows Store pouvant authentifier les utilisateurs, appeler en toute sécurité les API web à l’aide d’OAuth 2.0 et obtenir des informations de base concernant l’utilisateur.

Si vous n’avez pas encore rempli votre locataire avec des utilisateurs, il est maintenant temps de le faire.
1. Exécutez votre application DirectorySearcher, puis connectez-vous avec l’un des utilisateurs.
2. Recherchez d’autres utilisateurs en fonction de leur UPN.
3. Fermez l’application et réexécutez-la. Observez que la session utilisateur reste identique.
4. Déconnectez-vous par un clic droit pour afficher la barre inférieure, puis reconnectez-vous sous le nom d’un autre utilisateur.

La bibliothèque ADAL facilite l’intégration de toutes ces fonctionnalités d’identité communes dans l’application. Elle effectue les tâches ingrates pour vous, telles que gestion du cache, prise en charge du protocole OAuth, présentation d’une interface utilisateur de connexion à l’utilisateur et actualisation des jetons expirés. Vous n’avez besoin de connaître qu’un seul appel d’API, `authContext.AcquireToken*(…)`.

Pour référence, téléchargez [l’exemple terminé](https://github.com/AzureADQuickStarts/NativeClient-WindowsStore/archive/complete.zip) (sans vos valeurs de configuration).

Vous pouvez à présent aborder d’autres scénarios d’identité. Par exemple, essayez de [Sécuriser une API web .NET avec Azure AD](active-directory-devquickstarts-webapi-dotnet.md).

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
