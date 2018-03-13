---
title: Application native .NET Azure Active Directory v2.0 | Microsoft Docs
description: "Comment créer une application native .NET qui se connecte avec les comptes Microsoft personnels, ainsi qu’avec les comptes professionnels ou scolaires."
services: active-directory
documentationcenter: 
author: jmprieur
manager: mtillman
editor: 
ms.assetid: 46d81e09-bad0-44ce-9026-881805976e72
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 07/30/2016
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 88679e7dd71011f767cbe4de295c284516375d20
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
# <a name="add-sign-in-to-a-windows-desktop-app"></a>Ajouter une connexion à une application de bureau Windows
Le point de terminaison v2.0 vous permet de rapidement ajouter une authentification à vos applications de bureau avec la prise en charge des comptes Microsoft personnels, ainsi que les comptes professionnels ou scolaires.  Il permet également à votre application de communiquer de manière sécurisée avec une API web principale, ainsi qu’avec [Microsoft Graph](https://graph.microsoft.io) et un nombre limité [d’API unifiées Office 365](https://www.msdn.com/office/office365/howto/authenticate-Office-365-APIs-using-v2).

> [!NOTE]
> Les scénarios et les fonctionnalités Azure Active Directory (AD) ne sont pas tous pris en charge par le point de terminaison v2.0.  Pour déterminer si vous devez utiliser le point de terminaison v2.0, consultez les [limitations de v2.0](active-directory-v2-limitations.md).
> 
> 

Pour les [applications natives .NET qui s’exécutent sur un appareil](active-directory-v2-flows.md#mobile-and-native-apps), Azure AD fournit la bibliothèque d’authentification Microsoft Identity (bibliothèque MSAL).  Le seul objectif de cette bibliothèque MSAL est de faciliter l’obtention de jetons pour votre application, qui les utilise pour appeler les services Web.  Pour illustrer sa facilité d’utilisation, nous allons créer une application de liste des tâches .NET WPF qui exécute les activités suivantes :

* connexion de l’utilisateur et obtention des jetons d’accès à l’aide du [protocole d’authentification OAuth 2.0](active-directory-v2-protocols.md) ;
* appel sécurisé d’un service Web principal de liste de tâches, qui est également sécurisé par OAuth 2.0 ;
* déconnexion de l’utilisateur.

## <a name="download-sample-code"></a>Télécharger l’exemple de code
Le code associé à ce didacticiel est stocké [sur GitHub](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet).  Pour suivre la procédure, vous pouvez [télécharger la structure de l’application au format .zip](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/skeleton.zip) ou la cloner :

    git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git

L'application terminée est également fournie à la fin de ce didacticiel.

## <a name="register-an-app"></a>Inscription d’une application
Créez une application à l’adresse [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), ou suivez cette [procédure détaillée](active-directory-v2-app-registration.md).  Veillez à respecter les points suivants :

* copier l' **ID d'application** attribué à votre application, vous en aurez bientôt besoin ;
* ajouter la plateforme **Mobile** pour votre application ;

## <a name="install--configure-msal"></a>Installation et configuration de la bibliothèque MSAL
Maintenant que vous disposez d’une application enregistrée auprès de Microsoft, vous pouvez installer la bibliothèque MSAL et écrire votre code associé aux identités.  Pour permettre à la bibliothèque MSAL de communiquer avec le point de terminaison v2.0, vous devez lui fournir des informations sur l’enregistrement de votre application.

* Commencez par ajouter la bibliothèque MSAL au projet TodoListClient à l’aide de la console du gestionnaire de package.

```
PM> Install-Package Microsoft.Identity.Client -ProjectName TodoListClient -IncludePrerelease
```

* Dans le projet TodoListClient, ouvrez `app.config`.  Remplacez les valeurs des éléments de la section `<appSettings>` afin qu’elles reflètent les valeurs saisies dans le portail d’inscription d’applications.  Votre code se réfère à ces valeurs chaque fois qu’il utilise la bibliothèque MSAL.
  
  * L’élément `ida:ClientId` est l’ **ID d’application** de l’application copiée à partir du portail.
* Dans le projet TodoList-Service, ouvrez l’élément `web.config` dans la racine du projet.  
  
  * Remplacez la valeur `ida:Audience` par l’ **ID d’application** du portail.

## <a name="use-msal-to-get-tokens"></a>Utilisation de la bibliothèque MSAL pour obtenir des jetons
Le principe de base de la bibliothèque MSAL consiste simplement à appeler `app.AcquireToken(...)`chaque fois que votre application a besoin d’un jeton d’accès, et la bibliothèque MSAL s’occupe du reste.  

* Dans le projet `TodoListClient`, ouvrez `MainWindow.xaml.cs` et recherchez la méthode `OnInitialized(...)`.  La première étape consiste à initialiser la `PublicClientApplication` de votre application, c’est-à-dire la classe principale de la bibliothèque MSAL représentant les applications natives.  C’est à ce moment-là que vous fournissez à la bibliothèque MSAL les coordonnées dont elle a besoin pour communiquer avec Azure AD et lui indiquer comment mettre en cache des jetons.

```csharp
protected override async void OnInitialized(EventArgs e)
{
        base.OnInitialized(e);

        app = new PublicClientApplication(new FileCache());
        AuthenticationResult result = null;
        ...
}
```

* Lorsque l’application démarre, nous souhaitons vérifier si l’utilisateur est déjà connecté.  Toutefois, nous ne voulons pas encore invoquer d’interface utilisateur de connexion. Nous demanderons à l’utilisateur de cliquer sur l’option de connexion.  Également dans la méthode `OnInitialized(...)` :

```csharp
// As the app starts, we want to check to see if the user is already signed in.
// You can do so by trying to get a token from MSAL, using the method
// AcquireTokenSilent.  This forces MSAL to throw an exception if it cannot
// get a token for the user without showing a UI.
try
{
    result = await app.AcquireTokenSilentAsync(new string[] { clientId });
    // If we got here, a valid token is in the cache - or MSAL was able to get a new oen via refresh token.
    // Proceed to fetch the user's tasks from the TodoListService via the GetTodoList() method.

    SignInButton.Content = "Clear Cache";
    GetTodoList();
}
catch (MsalException ex)
{
    if (ex.ErrorCode == "user_interaction_required")
    {
        // If user interaction is required, the app should take no action,
        // and simply show the user the sign in button.
    }
    else
    {
        // Here, we catch all other MsalExceptions
        string message = ex.Message;
        if (ex.InnerException != null)
        {
            message += "Inner Exception : " + ex.InnerException.Message;
        }
        MessageBox.Show(message);
    }
}

```

* Si l’utilisateur, non connecté, clique sur le bouton de connexion, nous souhaitons invoquer une interface utilisateur de connexion et lui demander de saisir ses informations d’identification.  Implémentez le gestionnaire du bouton de connexion :

```csharp
private async void SignIn(object sender = null, RoutedEventArgs args = null)
{
        // TODO: Sign the user out if they clicked the "Clear Cache" button

// If the user clicked the 'Sign-In' button, force
// MSAL to prompt the user for credentials by using
// AcquireTokenAsync, a method that is guaranteed to show a prompt to the user.
// MSAL will get a token for the TodoListService and cache it for you.

AuthenticationResult result = null;
try
{
    result = await app.AcquireTokenAsync(new string[] { clientId });
    SignInButton.Content = "Clear Cache";
    GetTodoList();
}
catch (MsalException ex)
{
    // If MSAL cannot get a token, it will throw an exception.
    // If the user canceled the login, it will result in the
    // error code 'authentication_canceled'.

    if (ex.ErrorCode == "authentication_canceled")
    {
        MessageBox.Show("Sign in was canceled by the user");
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


}
```

* Si l’utilisateur parvient à se connecter, la bibliothèque MSAL reçoit et met en cache un jeton pour vous. Vous pouvez appeler la méthode `GetTodoList()` en toute confiance.  Pour récupérer les tâches d’un utilisateur, il ne vous reste plus qu’à implémenter la méthode `GetTodoList()`.

```csharp
private async void GetTodoList()
{

AuthenticationResult result = null;
try
{
    // Here, we try to get an access token to call the TodoListService
    // without invoking any UI prompt.  AcquireTokenSilentAsync forces
    // MSAL to throw an exception if it cannot get a token silently.


    result = await app.AcquireTokenSilentAsync(new string[] { clientId });
}
catch (MsalException ex)
{
    // MSAL couldn't get a token silently, so show the user a message
    // and let them click the Sign-In button.

    if (ex.ErrorCode == "user_interaction_required")
    {
        MessageBox.Show("Please sign in first");
        SignInButton.Content = "Sign In";
    }
    else
    {
        // In any other case, an unexpected error occurred.

        string message = ex.Message;
        if (ex.InnerException != null)
        {
            message += "Inner Exception : " + ex.InnerException.Message;
        }
        MessageBox.Show(message);
    }

    return;
}

// Once the token has been returned by MSAL,
// add it to the http authorization header,
// before making the call to access the To Do list service.

httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.Token);


        ...
...


- When the user is done managing their To-Do List, they may finally sign out of the app by clicking the "Clear Cache" button.

```csharp
private async void SignIn(object sender = null, RoutedEventArgs args = null)
{
        // If the user clicked the 'clear cache' button,
        // clear the MSAL token cache and show the user as signed out.
        // It's also necessary to clear the cookies from the browser
        // control so the next user has a chance to sign in.

        if (SignInButton.Content.ToString() == "Clear Cache")
        {
                TodoList.ItemsSource = string.Empty;
                app.UserTokenCache.Clear(app.ClientId);
                ClearCookies();
                SignInButton.Content = "Sign In";
                return;
        }

        ...
```

## <a name="run"></a>Exécuter
Félicitations ! Vous disposez désormais d’une application .NET WPF fonctionnelle, capable d’authentifier les utilisateurs et d’appeler des API web en toute sécurité via OAuth 2.0.  Exécutez vos projets et connectez-vous avec un compte Microsoft personnel ou un compte professionnel ou scolaire.  Ajoutez des tâches à la liste de tâches de cet utilisateur.  Déconnectez-vous et reconnectez-vous à l'aide du compte d'un autre utilisateur pour afficher sa liste de tâches.   Fermez l’application et exécutez-la de nouveau.  Remarquez à quel point la session de l’utilisateur reste intacte (cela s’explique par le fait que l’application cache des jetons dans un fichier local).

La bibliothèque MSAL permet d’intégrer facilement des fonctionnalités d’identité courantes à votre application à l’aide de vos comptes personnels et professionnels.  Elle effectue les tâches ingrates pour vous : gestion du cache, prise en charge du protocole OAuth, présentation d’une interface utilisateur de connexion à l’utilisateur, actualisation des jetons expirés et bien plus encore.  La seule chose que vous devez vraiment connaître est un appel unique d’API : `app.AcquireTokenAsync(...)`.

Pour référence, l’exemple terminé (sans vos valeurs de configuration) [est fourni ici au format .zip](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/complete.zip). Vous pouvez également le cloner à partir de GitHub :

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git```

## <a name="next-steps"></a>Étapes suivantes
Vous pouvez maintenant aborder des rubriques plus sophistiquées.  Par exemple :

* [Sécurisation de l’API web TodoListService avec le point de terminaison v2.0](active-directory-v2-devquickstarts-dotnet-api.md)

Pour obtenir des ressources supplémentaires, consultez :  

* [Guide du développeur 2.0 &gt;&gt;](active-directory-appmodel-v2-overview.md)
* [Balise « msal » StackOverflow &gt;&gt;](http://stackoverflow.com/questions/tagged/msal)

## <a name="get-security-updates-for-our-products"></a>Obtenir les mises à jour de sécurité de nos produits
Nous vous encourageons à activer les notifications d’incidents de sécurité en vous rendant sur [cette page](https://technet.microsoft.com/security/dd252948) et en vous abonnant aux alertes d’avis de sécurité.

