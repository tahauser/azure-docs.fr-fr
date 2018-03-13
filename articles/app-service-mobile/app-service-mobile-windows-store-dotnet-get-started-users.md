---
title: "Ajout de authentification à votre plateforme Windows universelle (UWP) | Microsoft Docs"
description: "Découvrez comment utiliser Azure App Service Mobile Apps pour authentifier les utilisateurs de votre application de plateforme Windows universelle à l’aide de divers fournisseurs d'identité, notamment AAD, Google, Facebook, Twitter et Microsoft."
services: app-service\mobile
documentationcenter: windows
author: conceptdev
manager: panarasi
editor: 
ms.assetid: 6cffd951-893e-4ce5-97ac-86e3f5ad9466
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 07/05/2017
ms.author: panarasi
ms.openlocfilehash: 4cc597f8aca13445034c8a1691b41018d4d9bc4b
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2018
---
# <a name="add-authentication-to-your-windows-app"></a>Ajout de l'authentification à votre application Windows
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

Cette rubrique indique comment ajouter l'authentification basée sur le cloud à votre application mobile. Dans ce didacticiel, vous ajoutez l'authentification au projet de démarrage rapide de plateforme Windows universelle pour Mobile Apps à l'aide d'un fournisseur d'identité pris en charge par Azure App Service. Une fois l'utilisateur authentifié et autorisé par votre backend d'application Mobile, la valeur de l'ID utilisateur s'affiche.

Ce didacticiel est basé sur le démarrage rapide de Mobile Apps. Vous devez commencer par suivre le didacticiel [Prise en main de Mobile Apps](app-service-mobile-windows-store-dotnet-get-started.md).

## <a name="register"></a>Inscription de votre application pour l’authentification et configuration d’App Service
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Ajouter votre application aux URL de redirection externes autorisées

L’authentification sécurisée nécessite de définir un nouveau schéma d’URL pour votre application. Cela permet au système d’authentification de vous rediriger vers votre application une fois le processus d’authentification terminé. Dans ce didacticiel, nous utilisons le schéma d’URL _appname_. Toutefois, vous pouvez utiliser le schéma d’URL de votre choix. Il doit être propre à votre application mobile. Pour activer la redirection côté serveur, procédez comme suit :

1. Dans le [portail Azure], sélectionnez votre instance App Service.

2. Cliquez sur l’option de menu **Authentication/Authorisation**.

3. Dans **URL de redirection externes autorisées**, saisissez `url_scheme_of_your_app://easyauth.callback`.  La chaîne **url_scheme_of_your_app** de cette chaîne est le schéma d’URL de votre application mobile.  Elle doit être conforme à la spécification d’URL normale pour un protocole (utiliser des lettres et des chiffres uniquement et commencer par une lettre).  Vous devez noter la chaîne que vous choisissez, dans la mesure où vous devez ajuster votre code d’application mobile avec le schéma d’URL à plusieurs endroits.

4. Cliquez sur **OK**.

5. Cliquez sur **Enregistrer**.

## <a name="permissions"></a>Restriction des autorisations pour les utilisateurs authentifiés
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

À présent, vous pouvez vérifier que l’accès anonyme à votre serveur principal a été désactivé. Avec le projet d’application Windows défini en tant que projet de démarrage, déployez et exécutez l'application, et vérifiez qu'une exception non prise en charge avec le code d'état 401 (Non autorisé) est déclenchée après le démarrage de l'application. Cette exception se produit, car l’application tente d’accéder à votre code Mobile App en tant qu’utilisateur non authentifié, alors que la table *TodoItem* requiert désormais une authentification.

Ensuite, vous allez mettre à jour l'application pour authentifier les utilisateurs avant de demander des ressources à partir de votre service App Service.

## <a name="add-authentication"></a>Ajout de l’authentification à l’application
1. Dans le fichier de projet d’application UWP MainPage.xaml.cs, ajoutez l’extrait de code suivant :
   
        // Define a member variable for storing the signed-in user. 
        private MobileServiceUser user;
   
        // Define a method that performs the authentication process
        // using a Facebook sign-in. 
        private async System.Threading.Tasks.Task<bool> AuthenticateAsync()
        {
            string message;
            bool success = false;
            try
            {
                // Change 'MobileService' to the name of your MobileServiceClient instance.
                // Sign-in using Facebook authentication.
                user = await App.MobileService
                    .LoginAsync(MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
                message =
                    string.Format("You are now signed in - {0}", user.UserId);
   
                success = true;
            }
            catch (InvalidOperationException)
            {
                message = "You must log in. Login Required";
            }
   
            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
            return success;
        }
   
    Ce code authentifie l’utilisateur à l’aide d’une connexion Facebook. Si vous utilisez un fournisseur d'identité différent de Facebook, remplacez la valeur de **MobileServiceAuthenticationProvider** ci-dessus par la valeur de votre fournisseur.
2. Remplacez la méthode **OnNavigatedTo()** dans MainPage.xaml.cs. Ensuite, vous allez ajouter à l’application un bouton **Connexion** qui déclenche l’authentification.

        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            if (e.Parameter is Uri)
            {
                App.MobileService.ResumeWithURL(e.Parameter as Uri);
            }
        }

3. Ajoutez l’extrait de code suivant dans MainPage.xaml.cs :
   
        private async void ButtonLogin_Click(object sender, RoutedEventArgs e)
        {
            // Login the user and then load data from the mobile app.
            if (await AuthenticateAsync())
            {
                // Switch the buttons and load items from the mobile app.
                ButtonLogin.Visibility = Visibility.Collapsed;
                ButtonSave.Visibility = Visibility.Visible;
                //await InitLocalStoreAsync(); //offline sync support.
                await RefreshTodoItems();
            }
        }
4. Ouvrez le fichier de projet MainPage.xaml, recherchez l’élément qui définit le bouton **Enregistrer** et remplacez-le par le code suivant :
   
        <Button Name="ButtonSave" Visibility="Collapsed" Margin="0,8,8,0" 
                Click="ButtonSave_Click">
            <StackPanel Orientation="Horizontal">
                <SymbolIcon Symbol="Add"/>
                <TextBlock Margin="5">Save</TextBlock>
            </StackPanel>
        </Button>
        <Button Name="ButtonLogin" Visibility="Visible" Margin="0,8,8,0" 
                Click="ButtonLogin_Click" TabIndex="0">
            <StackPanel Orientation="Horizontal">
                <SymbolIcon Symbol="Permissions"/>
                <TextBlock Margin="5">Sign in</TextBlock> 
            </StackPanel>
        </Button>
5. Ajoutez l’extrait de code suivant dans App.xaml.cs :

        protected override void OnActivated(IActivatedEventArgs args)
        {
            if (args.Kind == ActivationKind.Protocol)
            {
                ProtocolActivatedEventArgs protocolArgs = args as ProtocolActivatedEventArgs;
                Frame content = Window.Current.Content as Frame;
                if (content.Content.GetType() == typeof(MainPage))
                {
                    content.Navigate(typeof(MainPage), protocolArgs.Uri);
                }
            }
            Window.Current.Activate();
            base.OnActivated(args);
        }
6. Ouvrez le fichier Package.appxmanifest, accédez à **Déclarations**, dans la liste déroulante **Déclarations disponibles**, sélectionnez **Protocole** et cliquez sur le bouton **Ajouter**. À présent, configurez les **propriétés** de la déclaration **Protocole**. Dans **Nom d’affichage**, ajoutez le nom que vous souhaitez montrer aux utilisateurs de votre application. Dans **Nom**, ajoutez votre {url_scheme_of_your_app}.
7. Appuyez sur la touche F5 pour exécuter l'application, puis cliquez sur le bouton **Se connecter** pour vous connecter à l'application avec le fournisseur d'identité choisi. Lorsque vous êtes connecté, l'application s'exécute sans erreur, et vous pouvez exécuter des requêtes sur votre backend et mettre à jour les données.

## <a name="tokens"></a>Stockage du jeton d’authentification sur le client
L’exemple précédent montrait une connexion standard, qui nécessite que le client contacte le fournisseur d’identité et le service d’application à chaque démarrage de l’application. Cette méthode est non seulement inefficace, mais vous pouvez rencontrer des problèmes d'utilisation si de nombreux clients tentent de lancer votre application en même temps. Une meilleure approche consiste à mettre en cache le jeton d’autorisation renvoyé par votre service d’application et à l’utiliser en premier avant de faire appel à la connexion via un fournisseur.

> [!NOTE]
> Vous pouvez mettre en cache le jeton émis par App Services, que vous utilisiez l’authentification gérée par un client ou gérée par un service. Ce didacticiel utilise cette dernière.
> 
> 

[!INCLUDE [mobile-windows-universal-dotnet-authenticate-app-with-token](../../includes/mobile-windows-universal-dotnet-authenticate-app-with-token.md)]

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez terminé ce didacticiel sur l'authentification de base, vous pouvez passer à l'un des didacticiels suivants :

* [Ajouter des notifications Push à votre application Android](app-service-mobile-windows-store-dotnet-get-started-push.md)  
  Apprenez à ajouter la prise en charge des notifications Push à votre application et à configurer le serveur principal d’applications mobiles pour utiliser Azure Notification Hubs afin d’envoyer des notifications Push.
* [Activer la synchronisation hors connexion pour votre application](app-service-mobile-windows-store-dotnet-get-started-offline-data.md)  
  Apprenez à ajouter une prise en charge hors connexion à votre application à l’aide d’un serveur principal d’applications mobiles. La synchronisation hors connexion permet aux utilisateurs finaux d’interagir avec une application mobile &mdash;pour afficher, ajouter ou modifier des données&mdash;, même lorsqu’il n’existe aucune connexion réseau.

<!-- URLs. -->
[Get started with your mobile app]: app-service-mobile-windows-store-dotnet-get-started.md
