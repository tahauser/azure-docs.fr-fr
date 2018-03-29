---
title: Authentification, abonnement et modification du profil .NET Azure Active Directory B2C | Microsoft Docs
description: Comment créer une application de bureau Windows comprenant connexion, inscription et gestion de profil à l’aide d’Azure Active Directory B2C.
services: active-directory-b2c
documentationcenter: .net
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 01/07/2017
ms.author: davidmu
ms.openlocfilehash: ec679115259611eb05021a68a552d25777a73356
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-ad-b2c-build-a-windows-desktop-app"></a>Azure AD B2C : création d’une application de bureau Windows
Avec Azure Active Directory (Azure AD) B2C, vous pouvez ajouter de puissantes fonctionnalités de gestion des identités en libre-service à vos applications de bureau, en seulement quelques étapes. Cet article explique comment créer une application WPF (Windows Presentation Foundation) .NET de type « liste des tâches », qui inclut l’inscription d’utilisateur, la connexion et la gestion de profil. L’application prendra en charge l’inscription et la connexion à l’aide d’un nom d’utilisateur ou d’une adresse e-mail. Elle prendra également en charge l’inscription et la connexion à l’aide d’un compte de réseau social comme Facebook et Google.

## <a name="get-an-azure-ad-b2c-directory"></a>Obtention d'un répertoire Azure AD B2C
Avant de pouvoir utiliser Azure AD B2C, vous devez créer un répertoire ou un client.  Un répertoire est un conteneur destiné à recevoir tous vos utilisateurs, applications, groupes, etc. Si vous n’en possédez pas déjà un, [créez un répertoire B2C](active-directory-b2c-get-started.md) avant d’aller plus loin dans ce guide.

## <a name="create-an-application"></a>Création d'une application
Vous devez maintenant créer dans votre répertoire B2C une application fournissant à Azure AD certaines informations nécessaires pour communiquer de manière sécurisée avec votre application. Pour créer une application, suivez [ces instructions](active-directory-b2c-app-registration.md).  Veillez à effectuer les opérations suivantes :

* Incluez un **client natif** dans l’application.
* Copiez l’ **URI de redirection** `urn:ietf:wg:oauth:2.0:oob`. Il s’agit de l’URL par défaut pour cet exemple de code.
* Copiez **l’ID d’application** affecté à votre application. Vous en aurez besoin ultérieurement.

## <a name="create-your-policies"></a>Création de vos stratégies
Dans Azure AD B2C, chaque expérience utilisateur est définie par une [stratégie](active-directory-b2c-reference-policies.md). Cet exemple de code contient trois expériences liées à l’identité : l’inscription, la connexion et la modification du profil. Vous devez créer une stratégie pour chaque type, comme décrit dans [l’article de référence sur les stratégies](active-directory-b2c-reference-policies.md#create-a-sign-up-policy). Lors de la création des trois stratégies, assurez-vous de :

* Choisir **Inscription par le biais d’un ID utilisateur** ou **Inscription par le biais d’une adresse e-mail** dans le panneau des fournisseurs d’identité.
* Choisir **Nom d’affichage** et d’autres attributs d’inscription dans votre stratégie d’inscription.
* Choisir les revendications **Nom d’affichage** et **ID d’objet** comme revendications d’application pour chaque stratégie. Vous pouvez aussi choisir d'autres revendications.
* Copiez le **nom** de chaque stratégie après sa création. Il doit porter le préfixe `b2c_1_`.  Vous aurez besoin des noms de ces stratégies ultérieurement.

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Une fois vos trois stratégies créées, vous pouvez générer votre application.

## <a name="download-the-code"></a>Téléchargement du code
Le code associé à ce didacticiel [est stocké sur GitHub](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet). Pour générer l’exemple à mesure que vous avancez, vous pouvez [télécharger une structure de projet sous la forme d’un fichier .zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/skeleton.zip). Vous pouvez également cloner la structure :

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet.git
```

L’application terminée est également [disponible en tant que fichier .zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/complete.zip) ou sur la branche `complete` du même référentiel.

Une fois l’exemple de code téléchargé, ouvrez le fichier .sln Visual Studio pour commencer. Le projet `TaskClient` est l’application de bureau WPF avec laquelle l’utilisateur interagit. Dans le cadre de ce didacticiel, il appelle une API web de tâche principale, hébergée dans Azure, qui stocke la liste des actions de chaque utilisateur.  Vous n’avez pas besoin de créer l’API web, nous l’avons déjà exécutée pour vous.

Pour apprendre comment une API web authentifie en toute sécurité les demandes à l’aide d’Azure AD B2C, consultez [l’article sur la prise en main de l’API web](active-directory-b2c-devquickstarts-api-dotnet.md).

## <a name="execute-policies"></a>Exécuter des stratégies
Votre application communique avec Azure AD B2C en envoyant des messages d’authentification HTTP qui indiquent la stratégie à exécuter dans le cadre de la demande HTTP. Pour les applications de bureau .NET, vous pouvez utiliser la version préliminaire de la bibliothèque d’authentification Microsoft (MSAL) pour envoyer des messages d’authentification OAuth 2.0, exécuter des stratégies et obtenir des jetons qui appellent des API web.

### <a name="install-msal"></a>Installation de la bibliothèque MSAL
Ajoutez la bibliothèque MSAL au projet `TaskClient` à l’aide de la console du gestionnaire de package Visual Studio.

```
PM> Install-Package Microsoft.Identity.Client -IncludePrerelease
```

### <a name="enter-your-b2c-details"></a>saisissez les informations B2C
Ouvrez le fichier `Globals.cs` , puis remplacez chacune des valeurs de propriété par vos propres valeurs. Cette classe est utilisée dans `TaskClient` pour référencer des valeurs couramment utilisées.

```csharp
public static class Globals
{
    ...

    // TODO: Replace these five default with your own configuration values
    public static string tenant = "fabrikamb2c.onmicrosoft.com";
    public static string clientId = "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6";
    public static string signInPolicy = "b2c_1_sign_in";
    public static string signUpPolicy = "b2c_1_sign_up";
    public static string editProfilePolicy = "b2c_1_edit_profile";

    ...
}
```

[!INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

### <a name="create-the-publicclientapplication"></a>Création de la PublicClientApplication
La classe principale de la bibliothèque MSAL est `PublicClientApplication`. Cette classe représente votre application dans le système Azure AD B2C. Quand l’application démarre, créez une instance `PublicClientApplication` dans `MainWindow.xaml.cs`. Celle-ci peut être utilisée dans l’ensemble de la fenêtre.

```csharp
protected async override void OnInitialized(EventArgs e)
{
    base.OnInitialized(e);

    pca = new PublicClientApplication(Globals.clientId)
    {
        // MSAL implements an in-memory cache by default.  Since we want tokens to persist when the user closes the app,
        // we've extended the MSAL TokenCache and created a simple FileCache in this app.
        UserTokenCache = new FileCache(),
    };

    ...
```

### <a name="initiate-a-sign-up-flow"></a>Lancement d’un flux d'inscription
Quand un utilisateur choisit de s’inscrire, vous devez lancer un flux d’inscription qui utilise la stratégie d’inscription que vous avez créée. À l’aide de la bibliothèque MSAL, il vous suffit d’appeler `pca.AcquireTokenAsync(...)`. Les paramètres que vous transmettez à `AcquireTokenAsync(...)` déterminent, entre autres, le jeton que vous recevez et la stratégie utilisée dans la demande d’authentification.

```csharp
private async void SignUp(object sender, RoutedEventArgs e)
{
    AuthenticationResult result = null;
    try
    {
        // Use the app's clientId here as the scope parameter, indicating that
        // you want a token to the your app's backend web API (represented by
        // the cloud hosted task API).  Use the UiOptions.ForceLogin flag to
        // indicate to MSAL that it should show a sign-up UI no matter what.
        result = await pca.AcquireTokenAsync(new string[] { Globals.clientId },
                string.Empty, UiOptions.ForceLogin, null, null, Globals.authority,
                Globals.signUpPolicy);

        // Upon success, indicate in the app that the user is signed in.
        SignInButton.Visibility = Visibility.Collapsed;
        SignUpButton.Visibility = Visibility.Collapsed;
        EditProfileButton.Visibility = Visibility.Visible;
        SignOutButton.Visibility = Visibility.Visible;

        // When the request completes successfully, you can get user
        // information from the AuthenticationResult
        UsernameLabel.Content = result.User.Name;

        // After the sign up successfully completes, display the user's To-Do List
        GetTodoList();
    }

    // Handle any exeptions that occurred during execution of the policy.
    catch (MsalException ex)
    {
        if (ex.ErrorCode != "authentication_canceled")
        {
            // An unexpected error occurred.
            string message = ex.Message;
            if (ex.InnerException != null)
            {
                message += "Inner Exception : " + ex.InnerException.Message;
            }

            MessageBox.Show(message);
        }

        return;
    }
}
```

### <a name="initiate-a-sign-in-flow"></a>Lancement d’un flux de connexion
Vous pouvez lancer un flux de connexion de la même façon que vous lancez un flux d’inscription. Quand un utilisateur se connecte, effectuez le même appel à la bibliothèque MSAL, en utilisant cette fois votre stratégie de connexion :

```csharp
private async void SignIn(object sender = null, RoutedEventArgs args = null)
{
    AuthenticationResult result = null;
    try
    {
        result = await pca.AcquireTokenAsync(new string[] { Globals.clientId },
                    string.Empty, UiOptions.ForceLogin, null, null, Globals.authority,
                    Globals.signInPolicy);
        ...
```

### <a name="initiate-an-edit-profile-flow"></a>Lancement d’un flux de modification de profil
Là encore, vous pouvez exécuter une stratégie de modification de profil de la même manière :

```csharp
private async void EditProfile(object sender, RoutedEventArgs e)
{
    AuthenticationResult result = null;
    try
    {
        result = await pca.AcquireTokenAsync(new string[] { Globals.clientId },
                    string.Empty, UiOptions.ForceLogin, null, null, Globals.authority,
                    Globals.editProfilePolicy);
```

Dans tous ces cas, la bibliothèque MSAL retourne un jeton dans `AuthenticationResult` ou lève une exception. Chaque fois que vous obtenez un jeton de la bibliothèque MSAL, vous pouvez utiliser l’objet `AuthenticationResult.User` pour mettre à jour les données utilisateur dans l’application, telles que l’interface utilisateur. La bibliothèque ADAL met également en cache le jeton, pour une utilisation dans d’autres parties de l’application.

### <a name="check-for-tokens-on-app-start"></a>Vérification des jetons sur le démarrage de l’application
Vous pouvez également utiliser la bibliothèque MSAL pour effectuer le suivi de l’état de connexion de l’utilisateur.  Dans cette application, nous voulons que l’utilisateur reste connecté même après la fermeture et la réouverture de l’application.  Pour le remplacement de `OnInitialized`, utilisez la méthode `AcquireTokenSilent` de la bibliothèque MSAL pour rechercher les jetons mis en cache :

```csharp
AuthenticationResult result = null;
try
{
    // If the user has has a token cached with any policy, we'll display them as signed-in.
    TokenCacheItem tci = pca.UserTokenCache.ReadItems(Globals.clientId).Where(i => i.Scope.Contains(Globals.clientId) && !string.IsNullOrEmpty(i.Policy)).FirstOrDefault();
    string existingPolicy = tci == null ? null : tci.Policy;
    result = await pca.AcquireTokenSilentAsync(new string[] { Globals.clientId }, string.Empty, Globals.authority, existingPolicy, false);

    SignInButton.Visibility = Visibility.Collapsed;
    SignUpButton.Visibility = Visibility.Collapsed;
    EditProfileButton.Visibility = Visibility.Visible;
    SignOutButton.Visibility = Visibility.Visible;
    UsernameLabel.Content = result.User.Name;
    GetTodoList();
}
catch (MsalException ex)
{
    if (ex.ErrorCode == "failed_to_acquire_token_silently")
    {
        // There are no tokens in the cache.  Proceed without calling the To Do list service.
    }
    else
    {
        // An unexpected error occurred.
        string message = ex.Message;
        if (ex.InnerException != null)
        {
            message += "Inner Exception : " + ex.InnerException.Message;
        }
        MessageBox.Show(message);
    }
    return;
}
```

## <a name="call-the-task-api"></a>Appel de l’API de tâche
Vous avez maintenant utilisé la bibliothèque MSAL pour exécuter des stratégies et obtenir des jetons.  Si vous souhaitez utiliser un de ces jetons pour appeler l’API de tâche, vous pouvez utiliser à nouveau la méthode `AcquireTokenSilent` de la bibliothèque MSAL pour rechercher les jetons mis en cache :

```csharp
private async void GetTodoList()
{
    AuthenticationResult result = null;
    try
    {
        // Here we want to check for a cached token, independent of whatever policy was used to acquire it.
        TokenCacheItem tci = pca.UserTokenCache.ReadItems(Globals.clientId).Where(i => i.Scope.Contains(Globals.clientId) && !string.IsNullOrEmpty(i.Policy)).FirstOrDefault();
        string existingPolicy = tci == null ? null : tci.Policy;

        // Use AcquireTokenSilent to indicate that MSAL should throw an exception if a token cannot be acquired
        result = await pca.AcquireTokenSilentAsync(new string[] { Globals.clientId }, string.Empty, Globals.authority, existingPolicy, false);

    }
    // If a token could not be acquired silently, we'll catch the exception and show the user a message.
    catch (MsalException ex)
    {
        // There is no access token in the cache, so prompt the user to sign-in.
        if (ex.ErrorCode == "failed_to_acquire_token_silently")
        {
            MessageBox.Show("Please sign up or sign in first");
            SignInButton.Visibility = Visibility.Visible;
            SignUpButton.Visibility = Visibility.Visible;
            EditProfileButton.Visibility = Visibility.Collapsed;
            SignOutButton.Visibility = Visibility.Collapsed;
            UsernameLabel.Content = string.Empty;
        }
        else
        {
            // An unexpected error occurred.
            string message = ex.Message;
            if (ex.InnerException != null)
            {
                message += "Inner Exception : " + ex.InnerException.Message;
            }
            MessageBox.Show(message);
        }

        return;
    }
    ...
```

Quand l’appel à `AcquireTokenSilentAsync(...)` réussit et qu’un jeton est trouvé dans le cache, vous pouvez ajouter le jeton à l’en-tête `Authorization` de la demande HTTP. L’API web de tâche utilisera cet en-tête pour authentifier la demande afin de lire la liste des tâches de l’utilisateur :

```csharp
    ...
    // Once the token has been returned by MSAL, add it to the http authorization header, before making the call to access the To Do list service.
    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.Token);

    // Call the To Do list service.
    HttpResponseMessage response = await httpClient.GetAsync(Globals.taskServiceUrl + "/api/tasks");
    ...
```

## <a name="sign-the-user-out"></a>Déconnexion de l'utilisateur
Enfin, vous pouvez utiliser la bibliothèque MSAL pour mettre fin à la session d’un utilisateur dans l’application quand l’utilisateur sélectionne **Se déconnecter**.  À l’aide de la bibliothèque MSAL, vous pouvez effectuer cette opération en effaçant tous les jetons du cache de jetons :

```csharp
private void SignOut(object sender, RoutedEventArgs e)
{
    // Clear any remnants of the user's session.
    pca.UserTokenCache.Clear(Globals.clientId);

    // This is a helper method that clears browser cookies in the browser control that MSAL uses, it is not part of MSAL.
    ClearCookies();

    // Update the UI to show the user as signed out.
    TaskList.ItemsSource = string.Empty;
    SignInButton.Visibility = Visibility.Visible;
    SignUpButton.Visibility = Visibility.Visible;
    EditProfileButton.Visibility = Visibility.Collapsed;
    SignOutButton.Visibility = Visibility.Collapsed;
    return;
}
```

## <a name="run-the-sample-app"></a>Exécution de l'exemple d'application
Enfin, créez et exécutez l’exemple.  Inscrivez-vous à l’application à l’aide d’une adresse de messagerie ou d’un nom d’utilisateur. Déconnectez-vous, puis reconnectez-vous en tant que même utilisateur. Modifiez le profil de cet utilisateur. Déconnectez-vous et connectez-vous en utilisant un utilisateur différent.

## <a name="add-social-idps"></a>Ajout d’IDP sociaux
Actuellement, l’application prend uniquement en charge l’inscription et la connexion d’utilisateurs qui utilisent des **comptes locaux**. Il s’agit de comptes stockés dans votre répertoire B2C qui utilisent un nom d’utilisateur et un mot de passe. Avec Azure AD B2C, vous pouvez ajouter la prise en charge d’autres fournisseurs d’identité sans modifier votre code.

Pour ajouter des fournisseurs d’identité sociaux à votre application, commencez par suivre les instructions détaillées figurant dans ces articles. Pour chaque fournisseur d’identité que vous souhaitez prendre en charge, vous devez inscrire une application dans ce système et obtenir un ID client.

* [Définir Facebook en tant qu’IDP](active-directory-b2c-setup-fb-app.md)
* [Définir Google en tant qu’IDP](active-directory-b2c-setup-goog-app.md)
* [Définir Amazon en tant qu’IDP](active-directory-b2c-setup-amzn-app.md)
* [Définir LinkedIn en tant qu’IDP](active-directory-b2c-setup-li-app.md)

Après avoir ajouté les fournisseurs d’identité à votre répertoire B2C, vous devez modifier chacune de vos trois stratégies pour inclure ces nouveaux fournisseurs, comme décrit dans [l’article de référence sur les stratégies](active-directory-b2c-reference-policies.md). Après avoir enregistré vos stratégies, exécutez à nouveau l’application. Les nouveaux fournisseurs d’identité doivent être ajoutés comme options d’inscription et de connexion dans chacune de vos expériences relatives à l’identité.

Vous pouvez tester vos stratégies et observer les résultats sur votre exemple d’application. Ajoutez ou supprimez des fournisseurs d’identité, manipulez des revendications d’application ou modifiez des attributs d’inscription. Faites des essais jusqu’à ce que vous compreniez la façon dont les stratégies, les demandes d’authentification et la bibliothèque MSAL sont liées.

Pour référence, l’exemple terminé [est fourni en tant que fichier .zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/complete.zip). Vous pouvez également le cloner à partir de GitHub :

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet.git```
