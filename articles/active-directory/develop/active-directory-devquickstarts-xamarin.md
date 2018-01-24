---
title: "Bien démarrer avec Azure AD Xamarin | Microsoft Docs"
description: "Créez des applications Xamarin qui s’intègrent avec Azure AD pour la connexion et appellent des API protégées par Azure AD en utilisant OAuth."
services: active-directory
documentationcenter: xamarin
author: jmprieur
manager: mtillman
editor: 
ms.assetid: 198cd2c3-f7c8-4ec2-b59d-dfdea9fe7d95
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: article
ms.date: 11/30/2017
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: e3d0a07323189599cb86dd2bf1347c2107efa842
ms.sourcegitcommit: 234c397676d8d7ba3b5ab9fe4cb6724b60cb7d25
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/20/2017
---
# <a name="azure-ad-xamarin-getting-started"></a>Bien démarrer avec Azure AD Xamarin
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Avec Xamarin, vous pouvez écrire des applications mobiles en C# qui peuvent s’exécuter sous iOS, Android et Windows (appareils mobiles et PC). Si vous créez une application avec Xamarin, Azure Active Directory (Azure AD) facilite l’authentification des utilisateurs avec leur compte Azure AD. L’application peut également utiliser en toute sécurité une API web protégée par Azure AD, telle que les API Office 365 ou l’API Azure.

Pour les applications Xamarin qui doivent accéder à des ressources protégées, Azure AD fournit la bibliothèque d’authentification Active Directory (ADAL). Cette bibliothèque a pour seule fonction de simplifier l’obtention des jetons d’accès pour les applications. Pour illustrer cette simplicité, cet article montre comment créer des applications DirectorySearcher effectuant les actions suivantes :

* exécution sous iOS, Android, Windows Desktop, Windows Phone et Windows Store ;
* utilisation d’une bibliothèque de classes portable (PCL) unique pour authentifier les utilisateurs et obtenir des jetons pour l’API Graph Azure AD ;
* recherche, dans un annuaire, d’utilisateurs correspondant à un UPN (nom d’utilisateur principal) donné.

## <a name="before-you-get-started"></a>Avant de commencer
* Téléchargez [la structure du projet](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/skeleton.zip) ou [l’exemple terminé](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/complete.zip). Chaque téléchargement est une solution Visual Studio 2013.
* Vous avez également besoin d’un client Azure AD dans lequel créer les utilisateurs et inscrire l’application. Si vous ne disposez pas encore d’un client, [découvrez comment en obtenir un](active-directory-howto-tenant.md).

Lorsque vous êtes prêt, suivez les procédures des quatre sections qui suivent.

## <a name="step-1-set-up-your-xamarin-development-environment"></a>Étape 1 : Configurer votre environnement de développement Xamarin
Étant donné que ce didacticiel inclut des projets pour iOS, Android et Windows, il vous faut à la fois Visual Studio et Xamarin. Pour créer l’environnement nécessaire, suivez la procédure à la page [Configurer et installer Visual Studio et Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) sur MSDN. Les instructions incluent des documents que vous pouvez consulter pour en savoir plus sur Xamarin en attendant que les installations se terminent.

Une fois vous avez terminé la configuration, ouvrez la solution dans Visual Studio. Vous y trouverez six projets : cinq projets propres à la plateforme et une PCL, DirectorySearcher.cs, partagée par toutes les plateformes.

## <a name="step-2-register-the-directorysearcher-app"></a>Étape 2 : Inscrire l’application DirectorySearcher
Pour autoriser l’application à obtenir des jetons, vous devez tout d’abord l’inscrire dans votre client Azure AD et lui accorder l’autorisation d’accéder à l’API Graph Azure AD. Voici comment procéder :

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Dans la barre supérieure, cliquez sur votre compte. Puis, dans la liste **Annuaire**, sélectionnez le client Active Directory dans lequel vous voulez inscrire l’application.
3. Dans le volet gauche, cliquez sur **Plus de services**, puis sélectionnez **Azure Active Directory**.
4. Cliquez sur **Inscriptions des applications**, puis sélectionnez **Ajouter**.
5. Pour créer une **application cliente native**, suivez les invites.
  * Le champ **Nom** décrit l’application aux utilisateurs.
  * **L’URI de redirection** est une combinaison de schémas et de chaînes qu’Azure AD utilise pour renvoyer des réponses concernant les jetons. Entrez une valeur (par exemple, http://DirectorySearcher).
6. Une fois l’inscription terminée, Azure AD affecte un ID d’application unique à l’application. Copiez la valeur de l’onglet **Application**, car vous en aurez besoin ultérieurement.
7. Sur la page **Paramètres**, sélectionnez **Autorisations requises**, puis **Ajouter**.
8. Sélectionnez l’API **Microsoft Graph**. Sous **Autorisations déléguées**, ajoutez l’autorisation **Lire les données de l’annuaire**.  
Cette action permet à l’application d’interroger l’API Graph pour les utilisateurs.

## <a name="step-3-install-and-configure-adal"></a>Étape 3 : Installer et configurer la bibliothèque ADAL
Maintenant que vous disposez d’une application dans Azure AD, vous pouvez installer la bibliothèque ADAL et écrire votre code lié à l’identité. Pour que la bibliothèque ADAL puisse communiquer avec Azure AD, fournissez-lui des informations sur l’inscription des applications.

1. Ajoutez ADAL au projet DirectorySearcher à l’aide de la console du gestionnaire de package.

    `
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirectorySearcherLib
    `

    `
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Android
    `

    `
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Desktop
    `

    `
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-iOS
    `

    `
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Universal
    `

    Notez que deux références de bibliothèque sont ajoutées à chaque projet : la partie PCL de la bibliothèque ADAL et une partie propre à la plateforme.
2. Dans le projet DirectorySearcherLib, ouvrez DirectorySearcher.cs.
3. Remplacez les valeurs de membre de classe par les valeurs que vous avez entrées sur le Portail Azure. Votre code se réfère à ces valeurs chaque fois qu’il utilise la bibliothèque ADAL.

  * *tenant* est le domaine de votre client Azure AD (par exemple, contoso.onmicrosoft.com).
  * *clientId* est l’ID client de l’application, que vous avez copié à partir du portail.
  * *returnUri* est l’URI de redirection que vous avez entré sur le portail (par exemple, http://DirectorySearcher).

## <a name="step-4-use-adal-to-get-tokens-from-azure-ad"></a>Étape 4 : Utiliser la bibliothèque ADAL pour obtenir des jetons à partir d’Azure AD
Presque toute la logique d’authentification de l’application réside dans `DirectorySearcher.SearchByAlias(...)`. Dans les projets propres à la plateforme, la seule nécessité est de transmettre un paramètre contextuel à la PCL `DirectorySearcher`.

1. Ouvrez DirectorySearcher.cs et ajoutez un nouveau paramètre à la méthode `SearchByAlias(...)`. `IPlatformParameters` est le paramètre contextuel qui encapsule les objets propres à la plateforme que la bibliothèque ADAL doit authentifier.

    ```C#
    public static async Task<List<User>> SearchByAlias(string alias, IPlatformParameters parent)
    {
    ```

2. Initialisez `AuthenticationContext`, la classe principale de la bibliothèque ADAL.  
Cette action fournit à la bibliothèque ADAL les coordonnées dont elle a besoin pour communiquer avec Azure AD.
3. Appelez `AcquireTokenAsync(...)`, qui accepte l’objet `IPlatformParameters` et appelle le flux d’authentification nécessaire pour renvoyer un jeton à l’application.

    ```C#
    ...
        AuthenticationResult authResult = null;
        try
        {
            AuthenticationContext authContext = new AuthenticationContext(authority);
            authResult = await authContext.AcquireTokenAsync(graphResourceUri, clientId, returnUri, parent);
        }
        catch (Exception ee)
        {
            results.Add(new User { error = ee.Message });
            return results;
        }
    ...
    ```

    `AcquireTokenAsync(...)` tente tout d’abord de renvoyer un jeton pour la ressource demandée (l’API Graph dans ce cas) sans inviter les utilisateurs à entrer leurs informations d’identification (par le biais de la mise en cache ou de l’actualisation des anciens jetons). Si nécessaire, il affiche la page de connexion d’Azure AD aux utilisateurs avant d’acquérir le jeton demandé.
4. Joignez le jeton d’accès à la demande de l’API Graph, dans l’en-tête **Autorisation** :

    ```C#
    ...
        request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", authResult.AccessToken);
    ...
    ```

Le rôle de la PCL `DirectorySearcher` et du code lié à l’identité de l’application s’arrête là. Il ne reste qu’à appeler la méthode `SearchByAlias(...)` dans les affichages de chaque plateforme et, le cas échéant, à ajouter le code permettant de gérer correctement le cycle de vie de l’interface utilisateur.

### <a name="android"></a>Android
1. Dans MainActivity.cs, ajoutez un appel à `SearchByAlias(...)` dans le gestionnaire de clic de bouton :

    ```C#
    List<User> results = await DirectorySearcher.SearchByAlias(searchTermText.Text, new PlatformParameters(this));
    ```
2. Substituez la méthode du cycle de vie `OnActivityResult` pour rediriger toute authentification vers la méthode appropriée. La bibliothèque ADAL fournit une méthode d’assistance pour cela dans Android :

    ```C#
    ...
    protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
    {
        base.OnActivityResult(requestCode, resultCode, data);
        AuthenticationAgentContinuationHelper.SetAuthenticationAgentContinuationEventArgs(requestCode, resultCode, data);
    }
    ...
    ```

### <a name="windows-desktop"></a>Windows Desktop
Dans MainWindow.xaml.cs, appelez simplement `SearchByAlias(...)` en passant un `WindowInteropHelper` dans l’objet `PlatformParameters` du bureau :

```C#
List<User> results = await DirectorySearcher.SearchByAlias(
  SearchTermText.Text,
  new PlatformParameters(PromptBehavior.Auto, this.Handle));
```

#### <a name="ios"></a>iOS
Dans DirSearchClient_iOSViewController.cs, l’objet `PlatformParameters` iOS prend une référence au contrôleur d’affichage :

```C#
List<User> results = await DirectorySearcher.SearchByAlias(
  SearchTermText.Text,
  new PlatformParameters(PromptBehavior.Auto, this.Handle));
```

### <a name="windows-universal"></a>Windows Universal
Dans Windows Universal, ouvrez MainPage.xaml.cs et implémentez la méthode `Search`. Cette méthode utilise une méthode auxiliaire dans un projet partagé pour mettre à jour l’interface utilisateur en fonction des besoins.

```C#
...
List<User> results = await DirectorySearcherLib.DirectorySearcher.SearchByAlias(SearchTermText.Text, new PlatformParameters(PromptBehavior.Auto, false));
...
```

## <a name="whats-next"></a>Étapes suivantes
Vous disposez maintenant d’une application Xamarin fonctionnelle capable d’authentifier les utilisateurs et d’appeler en toute sécurité les API web à l’aide d’OAuth 2.0 sur cinq plateformes différentes.

Si vous n’avez pas encore rempli votre client avec des utilisateurs, il est maintenant temps de le faire.

1. Exécutez votre application DirectorySearcher, puis connectez-vous avec l’un des utilisateurs.
2. Recherchez d’autres utilisateurs en fonction de leur UPN.

La bibliothèque ADAL facilite l’intégration des fonctionnalités d’identité communes dans l’application. Elle effectue les tâches ingrates à votre place, notamment la gestion du cache, la prise en charge du protocole OAuth, la présentation d’une interface utilisateur de connexion à l’utilisateur et l’actualisation des jetons expirés. Vous n’avez besoin de connaître qu’un seul appel d’API, `authContext.AcquireToken*(…)`.

Pour référence, téléchargez [l’exemple terminé](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/complete.zip) (sans vos valeurs de configuration).

Vous pouvez à présent aborder d’autres scénarios d’identité. Par exemple, essayez de [Sécuriser une API web .NET avec Azure AD](active-directory-devquickstarts-webapi-dotnet.md).

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
