---
title: "Prise en main de l’authentification pour Mobile Apps dans l’application Xamarin Forms | Microsoft Docs"
description: "Découvrez comment utiliser Mobile Apps pour authentifier les utilisateurs de votre application Xamarin Forms via divers fournisseurs d'identité, notamment AAD, Google, Facebook, Twitter et Microsoft."
services: app-service\mobile
documentationcenter: xamarin
author: panarasi
manager: crdun
editor: 
ms.assetid: 9c55e192-c761-4ff2-8d88-72260e9f6179
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: article
ms.date: 08/07/2017
ms.author: panarasi
ms.openlocfilehash: e3e8c843437558c6d5d3a3c39bed1e647f852b18
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2018
---
# <a name="add-authentication-to-your-xamarin-forms-app"></a>Ajouter l’authentification à votre application Xamarin Forms
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="overview"></a>Vue d'ensemble
Cette rubrique montre comment authentifier les utilisateurs d'une application App Service Mobile App à partir de votre application cliente. Dans ce didacticiel, vous allez ajouter l’authentification au projet de démarrage rapide Xamarin Forms à l’aide d’un fournisseur d’identité pris en charge par App Service. Une fois l’utilisateur authentifié et autorisé par votre application Mobile App, la valeur de l’ID utilisateur s’affiche ; vous pouvez alors accéder aux données de table limitées.

## <a name="prerequisites"></a>Conditions préalables
Pour obtenir les meilleurs résultats avec ce didacticiel, nous vous recommandons de commencer par suivre le didacticiel [Créer une application Xamarin.Forms][1]. Après avoir terminé ce didacticiel, vous disposerez d’un projet Xamarin Forms qui est une application TodoList multiplateforme.

Si vous n’utilisez pas le projet de serveur du démarrage rapide téléchargé, vous devez ajouter le package d’extension d’authentification à votre projet. Pour plus d'informations sur les packages d'extension de serveur, consultez [Fonctionnement avec le Kit de développement logiciel (SDK) du serveur principal .NET pour Azure Mobile Apps][2].

## <a name="register-your-app-for-authentication-and-configure-app-services"></a>Inscription de votre application pour l'authentification et configuration d'App Services
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Ajouter votre application aux URL de redirection externes autorisées

L’authentification sécurisée nécessite de définir un nouveau schéma d’URL pour votre application. Cela permet au système d’authentification de vous rediriger vers votre application une fois le processus d’authentification terminé. Dans ce didacticiel, nous utilisons le schéma d’URL _appname_. Toutefois, vous pouvez utiliser le schéma d’URL de votre choix. Il doit être propre à votre application mobile. Pour activer la redirection côté serveur, procédez comme suit :

1. Dans le [portail Azure][8], sélectionnez votre instance App Service.

2. Cliquez sur l’option de menu **Authentication/Authorisation**.

3. Dans **URL de redirection externes autorisées**, saisissez `url_scheme_of_your_app://easyauth.callback`.  La chaîne **url_scheme_of_your_app** de cette chaîne est le schéma d’URL de votre application mobile.  Elle doit être conforme à la spécification d’URL normale pour un protocole (utiliser des lettres et des chiffres uniquement et commencer par une lettre).  Vous devez noter la chaîne que vous choisissez, dans la mesure où vous devez ajuster votre code d’application mobile avec le schéma d’URL à plusieurs endroits.

4. Cliquez sur **OK**.

5. Cliquez sur **Enregistrer**.

## <a name="restrict-permissions-to-authenticated-users"></a>Restriction des autorisations pour les utilisateurs authentifiés
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

## <a name="add-authentication-to-the-portable-class-library"></a>Ajout de l'authentification à la bibliothèque de classes portable
Mobile Apps utilise la méthode d’extension [LoginAsync][3] sur le [MobileServiceClient][4] pour connecter un utilisateur avec l’authentification App Service. Cet exemple utilise un flux d’authentification géré par le serveur qui affiche l’interface de connexion du fournisseur dans l’application. Pour plus d’informations, voir la rubrique [Authentification gérée par le serveur][5]. Pour offrir une meilleure expérience utilisateur dans votre application de production, vous devez plutôt envisager d’utiliser une [authentification gérée par le client][6].

Pour vous authentifier auprès d’un projet Xamarin Forms, définissez une interface **IAuthenticate** dans la bibliothèque de classes portable de l’application. Ensuite, ajoutez un bouton **Connexion** à l’interface utilisateur définie dans la bibliothèque de classes portable que l’utilisateur devra sélectionner pour démarrer l’authentification. Une fois l’authentification effectuée, les données sont chargées depuis le serveur principal d’application mobile.

Implémentez l’interface **IAuthenticate** pour chaque plateforme prise en charge par votre application.

1. Dans Visual Studio ou Xamarin Studio, ouvrez App.cs à partir du projet dont le nom contient le terme **Portable** et qui correspond à un projet de bibliothèque de classes portable. Ajoutez ensuite l’instruction `using` suivante :

        using System.Threading.Tasks;
2. Dans App.cs, ajoutez la définition d’interface `IAuthenticate` suivante immédiatement avant la définition de classe `App`.

        public interface IAuthenticate
        {
            Task<bool> Authenticate();
        }
3. Pour initialiser l’interface avec une implémentation propre à la plateforme, ajoutez les membres statiques suivants à la classe **App**.

        public static IAuthenticate Authenticator { get; private set; }

        public static void Init(IAuthenticate authenticator)
        {
            Authenticator = authenticator;
        }
4. Ouvrez TodoList.xaml à partir du projet de bibliothèque de classes portable, ajoutez l’élément **bouton** suivant dans l’élément de disposition *buttonsPanel* , après le bouton existant :

          <Button x:Name="loginButton" Text="Sign-in" MinimumHeightRequest="30"
            Clicked="loginButton_Clicked"/>

    Ce bouton déclenche l’authentification gérée par le serveur auprès du serveur principal de votre application mobile.
5. Ouvrez TodoList.xaml.cs à partir du projet de bibliothèque de classes portable, puis ajoutez le champ suivant à la classe `TodoList` :

        // Track whether the user has authenticated.
        bool authenticated = false;
6. Remplacez la méthode **OnAppearing** par le code suivant :

        protected override async void OnAppearing()
        {
            base.OnAppearing();

            // Refresh items only when authenticated.
            if (authenticated == true)
            {
                // Set syncItems to true in order to synchronize the data
                // on startup when running in offline mode.
                await RefreshItems(true, syncItems: false);

                // Hide the Sign-in button.
                this.loginButton.IsVisible = false;
            }
        }

    Ce code permet de s’assurer que les données seront actualisées par le service seulement une fois que vous vous serez authentifié.
7. Ajoutez le gestionnaire suivant pour l’événement **Clicked** à la classe **TodoList** :

        async void loginButton_Clicked(object sender, EventArgs e)
        {
            if (App.Authenticator != null)
                authenticated = await App.Authenticator.Authenticate();

            // Set syncItems to true to synchronize the data on startup when offline is enabled.
            if (authenticated == true)
                await RefreshItems(true, syncItems: false);
        }
8. Enregistrez vos modifications et régénérez le projet de bibliothèque de classes portable en vérifiant qu’il ne contient aucune erreur.

## <a name="add-authentication-to-the-android-app"></a>Ajout de l'authentification à l'application Android
Cette section montre comment implémenter l’interface **IAuthenticate** dans le projet d’application Android. Vous pouvez ignorer cette section si vous n’utilisez pas d’appareils Android.

1. Dans Visual Studio ou Xamarin Studio, cliquez avec le bouton droit sur le projet **droid**, puis cliquez sur **Définir comme projet de démarrage**.
2. Appuyez sur F5 pour démarrer le projet dans le débogueur, puis vérifiez qu’une exception non prise en charge avec le code d’état 401 (Unauthorized) est déclenchée après le démarrage de l’application. Ce code 401 se produit car l’accès au serveur principal est limité aux seuls utilisateurs autorisés.
3. Ouvrez MainActivity.cs dans le projet Android et ajoutez les instructions `using` suivantes :

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
4. Mettez à jour la classe **MainActivity** pour implémenter l’interface **IAuthenticate**, comme suit :

        public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsApplicationActivity, IAuthenticate
5. Mettez à jour la classe **MainActivity** en ajoutant un champ **MobileServiceUser** et une méthode **Authenticate** requise par l’interface **IAuthenticate**, comme suit :

        // Define a authenticated user.
        private MobileServiceUser user;

        public async Task<bool> Authenticate()
        {
            var success = false;
            var message = string.Empty;
            try
            {
                // Sign in with Facebook login using a server-managed flow.
                user = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(this, 
                    MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
                if (user != null)
                {
                    message = string.Format("you are now signed-in as {0}.",
                        user.UserId);
                    success = true;
                }
            }
            catch (Exception ex)
            {
                message = ex.Message;
            }

            // Display the success or failure message.
            AlertDialog.Builder builder = new AlertDialog.Builder(this);
            builder.SetMessage(message);
            builder.SetTitle("Sign-in result");
            builder.Create().Show();

            return success;
        }

    Si vous utilisez un fournisseur d’identité différent de Facebook, choisissez une autre valeur pour [MobileServiceAuthenticationProvider][7].

6. Mettez à jour le fichier **AndroidManifest.xml** en ajoutant le code XML suivant à l’intérieur de l’élément `<application>` :

    ```xml
    <activity android:name="com.microsoft.windowsazure.mobileservices.authentication.RedirectUrlActivity" android:launchMode="singleTop" android:noHistory="true">
      <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <data android:scheme="{url_scheme_of_your_app}" android:host="easyauth.callback" />
      </intent-filter>
    </activity>
    ```
    Remplacez `{url_scheme_of_your_app}` par votre modèle d’URL.
7. Ajoutez le code suivant à la méthode **OnCreate** de la classe **MainActivity** avant l’appel à `LoadApplication()` :

        // Initialize the authenticator before loading the app.
        App.Init((IAuthenticate)this);

    Ce code permet de garantir que l’authentificateur sera initialisé avant le chargement de l’application.
8. Recompilez et exécutez l’application, puis connectez-vous avec le fournisseur d’authentification choisi et vérifiez que vous êtes en mesure d’accéder aux données en tant qu’utilisateur authentifié.

## <a name="add-authentication-to-the-ios-app"></a>Ajout de l'authentification à l'application iOS
Cette section montre comment implémenter l’interface **IAuthenticate** dans le projet d’application iOS. Vous pouvez ignorer cette section si vous n’utilisez pas d’appareils iOS.

1. Dans Visual Studio ou Xamarin Studio, cliquez avec le bouton droit sur le projet **iOS**, puis cliquez sur**Définir comme projet de démarrage**.
2. Appuyez sur F5 pour démarrer le projet dans le débogueur, puis vérifiez qu’une exception non prise en charge avec le code d’état 401 (Unauthorized) est déclenchée après le démarrage de l’application. Cette réponse 401 se produit car l’accès au serveur principal est limité aux seuls utilisateurs autorisés.
3. Ouvrez AppDelegate.cs dans le projet iOS et ajoutez les instructions `using` suivantes :

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
4. Mettez à jour la classe **AppDelegate** pour implémenter l’interface **IAuthenticate**, comme suit :

        public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate, IAuthenticate
5. Mettez à jour la classe **AppDelegate** en ajoutant un champ **MobileServiceUser** et une méthode **Authenticate** requise par l’interface **IAuthenticate**, comme suit :

        // Define a authenticated user.
        private MobileServiceUser user;

        public async Task<bool> Authenticate()
        {
            var success = false;
            var message = string.Empty;
            try
            {
                // Sign in with Facebook login using a server-managed flow.
                if (user == null)
                {
                    user = await TodoItemManager.DefaultManager.CurrentClient
                        .LoginAsync(UIApplication.SharedApplication.KeyWindow.RootViewController,
                        MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
                    if (user != null)
                    {
                        message = string.Format("You are now signed-in as {0}.", user.UserId);
                        success = true;
                    }
                }
            }
            catch (Exception ex)
            {
               message = ex.Message;
            }

            // Display the success or failure message.
            UIAlertView avAlert = new UIAlertView("Sign-in result", message, null, "OK", null);
            avAlert.Show();

            return success;
        }

    Si vous utilisez un fournisseur d’identité différent de Facebook, choisissez une autre valeur pour [MobileServiceAuthenticationProvider].
    
6. Mettez à jour la classe **AppDelegate** en ajoutant la surcharge de méthode **OpenUrl**, comme suit :

        public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
        {
            return TodoItemManager.DefaultManager.CurrentClient.ResumeWithURL(url);
        }
   
7. Ajoutez la ligne de code suivante à la méthode **FinishedLaunching** avant l’appel à `LoadApplication()` :

        App.Init(this);

    Ce code permet de garantir que l’authentificateur sera initialisé avant le chargement de l’application.

8. Ouvrez le fichier Info.plist et ajoutez un **Type d’URL**. Définissez l’**Identificateur** sur un nom de votre choix, les **Schémas d’URL** sur le modèle d’URL pour votre application, et le **Rôle** sur Aucun.

9. Recompilez et exécutez l’application, puis connectez-vous avec le fournisseur d’authentification choisi et vérifiez que vous êtes en mesure d’accéder aux données en tant qu’utilisateur authentifié.

## <a name="add-authentication-to-windows-10-including-phone-app-projects"></a>Ajout de l’authentification à des projets d’application Windows 10 (y compris Windows Phone)
Cette section montre comment implémenter l’interface **IAuthenticate** dans les projets d’application Windows 10. Les mêmes étapes s’appliquent aux projets de plateforme Windows universelle (UWP), mais en utilisant le projet **UWP** (comportant des modifications indiquées). Vous pouvez ignorer cette section si vous n’utilisez pas d’appareils Windows.

1. Dans Visual Studio, cliquez avec le bouton droit sur le projet **UWP**, puis cliquez sur **Définir comme projet de démarrage**.
2. Appuyez sur F5 pour démarrer le projet dans le débogueur, puis vérifiez qu’une exception non prise en charge avec le code d’état 401 (Unauthorized) est déclenchée après le démarrage de l’application. Cette réponse 401 se produit car l’accès au serveur principal est limité aux seuls utilisateurs autorisés.
3. Ouvrez MainPage.xaml.cs dans le projet d’application Windows et ajoutez l’instruction `using` suivante :

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
        using Windows.UI.Popups;
        using <your_Portable_Class_Library_namespace>;

    Remplacez `<your_Portable_Class_Library_namespace>` par l’espace de noms de votre bibliothèque de classes portable.
4. Mettez à jour la classe **MainPage** pour implémenter l’interface **IAuthenticate**, comme suit :

        public sealed partial class MainPage : IAuthenticate
5. Mettez à jour la classe **MainPage** en ajoutant un champ **MobileServiceUser** et une méthode **Authenticate** requise par l’interface **IAuthenticate**, comme suit :

        // Define a authenticated user.
        private MobileServiceUser user;

        public async Task<bool> Authenticate()
        {
            string message = string.Empty;
            var success = false;

            try
            {
                // Sign in with Facebook login using a server-managed flow.
                if (user == null)
                {
                    user = await TodoItemManager.DefaultManager.CurrentClient
                        .LoginAsync(MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
                    if (user != null)
                    {
                        success = true;
                        message = string.Format("You are now signed-in as {0}.", user.UserId);
                    }
                }

            }
            catch (Exception ex)
            {
                message = string.Format("Authentication Failed: {0}", ex.Message);
            }

            // Display the success or failure message.
            await new MessageDialog(message, "Sign-in result").ShowAsync();

            return success;
        }

    Si vous utilisez un fournisseur d’identité différent de Facebook, choisissez une autre valeur pour [MobileServiceAuthenticationProvider][7].

1. Ajoutez la ligne de code suivante dans le constructeur de la classe **MainPage** avant l’appel à `LoadApplication()` :

        // Initialize the authenticator before loading the app.
        <your_Portable_Class_Library_namespace>.App.Init(this);

    Remplacez `<your_Portable_Class_Library_namespace>` par l’espace de noms de votre bibliothèque de classes portable.

3. Si vous utilisez **UWP**, ajoutez la substitution de méthode **OnActivated** suivante à la classe **App** :

       protected override void OnActivated(IActivatedEventArgs args)
       {
           base.OnActivated(args);

            if (args.Kind == ActivationKind.Protocol)
            {
                ProtocolActivatedEventArgs protocolArgs = args as ProtocolActivatedEventArgs;
                TodoItemManager.DefaultManager.CurrentClient.ResumeWithURL(protocolArgs.Uri);
            }
       }

3. Ouvrez le Package.appxmanifest et ajoutez une déclaration de **Protocole**. Définissez le **Nom d’affichage** sur un nom de votre choix, et le **Nom** sur le schéma d’URL pour votre application.

4. Recompilez et exécutez l’application, puis connectez-vous avec le fournisseur d’authentification choisi et vérifiez que vous êtes en mesure d’accéder aux données en tant qu’utilisateur authentifié.

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez terminé ce didacticiel sur l'authentification de base, vous pouvez passer à l'un des didacticiels suivants :

* [Ajouter des notifications Push à votre application Android](app-service-mobile-xamarin-forms-get-started-push.md)

  Apprenez à ajouter la prise en charge des notifications Push à votre application et à configurer le serveur principal d’applications mobiles pour utiliser Azure Notification Hubs afin d’envoyer des notifications Push.
* [Activer la synchronisation hors connexion pour votre application](app-service-mobile-xamarin-forms-get-started-offline-data.md)

  Apprenez à ajouter une prise en charge hors connexion à votre application à l’aide d’un serveur principal Mobile App. La synchronisation hors connexion permet aux utilisateurs finaux d’interagir avec une application mobile pour afficher, ajouter ou modifier des données, même lorsqu’il n’existe aucune connexion réseau.

<!-- Images. -->

<!-- URLs. -->
[1]: app-service-mobile-xamarin-forms-get-started.md
[2]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[3]: https://msdn.microsoft.com/library/azure/dn268341(v=azure.10).aspx
[4]: https://msdn.microsoft.com/library/azure/JJ553674(v=azure.10).aspx
[5]: app-service-mobile-dotnet-how-to-use-client-library.md#serverflow
[6]: app-service-mobile-dotnet-how-to-use-client-library.md#clientflow
[7]: https://msdn.microsoft.com/library/azure/jj730936(v=azure.10).aspx
[8]: https://portal.azure.com
