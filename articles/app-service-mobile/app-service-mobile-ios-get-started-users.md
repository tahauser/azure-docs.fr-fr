---
title: "Ajout de l’authentification sur iOS avec Azure Mobile Apps"
description: "Découvrez comment utiliser Azure Mobile Apps pour authentifier les utilisateurs de votre application iOS via divers fournisseurs d'identité, notamment AAD, Google, Facebook, Twitter et Microsoft."
services: app-service\mobile
documentationcenter: ios
author: conceptdev
manager: crdun
editor: 
ms.assetid: ef3d3cbe-e7ca-45f9-987f-80c44209dc06
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2017
ms.author: crdun
ms.openlocfilehash: e0eeee05ebad2e8148752f988bbbc2f6a0d7c296
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2018
---
# <a name="add-authentication-to-your-ios-app"></a>Ajout de l'authentification à votre application iOS
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

Dans ce didacticiel, vous allez ajouter l’authentification au projet de [Démarrage rapide iOS] en faisant appel à un fournisseur d’identité pris en charge. Ce didacticiel est basé sur le didacticiel [Démarrage rapide iOS] , que vous devez effectuer en premier.

## <a name="register"></a>Inscription de votre application pour l’authentification et configuration d’App Service
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Ajouter votre application aux URL de redirection externes autorisées

L’authentification sécurisée nécessite de définir un nouveau schéma d’URL pour votre application.  Cela permet au système d’authentification de vous rediriger vers votre application une fois le processus d’authentification terminé.  Dans ce didacticiel, nous utilisons le schéma d’URL _appname_.  Toutefois, vous pouvez utiliser le schéma d’URL de votre choix.  Il doit être propre à votre application mobile.  Pour activer la redirection côté serveur :

1. Dans le[ portail Azure], sélectionnez votre App Service.

2. Cliquez sur l’option de menu **Authentication/Authorisation**.

3. Sous la section **Fournisseurs d’authentification**, cliquez sur **Azure Active Directory**.

4. Définissez le **mode de gestion** sur **Avancé**.

5. Dans **URL de redirection externes autorisées**, saisissez `appname://easyauth.callback`.  La chaîne _appname_ de cette chaîne est le schéma d’URL de votre application mobile.  Elle doit être conforme à la spécification d’URL normale pour un protocole (utiliser des lettres et des chiffres uniquement et commencer par une lettre).  Vous devez noter la chaîne que vous choisissez, dans la mesure où vous devez ajuster votre code d’application mobile avec le schéma d’URL à plusieurs endroits.

6. Cliquez sur **OK**.

7. Cliquez sur **Enregistrer**.

## <a name="permissions"></a>Restriction des autorisations pour les utilisateurs authentifiés
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Dans Xcode, appuyez sur **Exécuter** pour démarrer l’application. Une exception se déclenche, car l’application essaye d’accéder au serveur principal en tant qu’utilisateur non authentifié alors que la table *TodoItem* requiert désormais l’authentification.

## <a name="add-authentication"></a>Ajout de l'authentification à l'application
**Objective-C**:

1. Sur votre Mac, ouvrez *QSTodoListViewController.m* dans Xcode et ajoutez la méthode suivante :

    ```Objective-C
    - (void)loginAndGetData
    {
        QSAppDelegate *appDelegate = (QSAppDelegate *)[UIApplication sharedApplication].delegate;
        appDelegate.qsTodoService = self.todoService;

        [self.todoService.client loginWithProvider:@"google" urlScheme:@"appname" controller:self animated:YES completion:^(MSUser * _Nullable user, NSError * _Nullable error) {
            if (error) {
                NSLog(@"Login failed with error: %@, %@", error, [error userInfo]);
            }
            else {
                self.todoService.client.currentUser = user;
                NSLog(@"User logged in: %@", user.userId);

                [self refresh];
            }
        }];
    }
    ```

    Remplacez *google* par *microsoftaccount*, *twitter*, *facebook* ou *windowsazureactivedirectory* si vous n’utilisez pas Google comme fournisseur d’identité. Si vous utilisez Facebook, vous devrez [autoriser les domaines Facebook][1] dans votre application.

    Remplacez **urlScheme** par un nom unique pour votre application.  La chaîne urlScheme doit être identique au protocole de schéma d’URL spécifié dans le champ **URL de redirection externes autorisés** dans le portail Azure. La chaîne urlScheme est utilisée par le rappel d’authentification pour revenir à votre application une fois la demande d’authentification terminée.

2. Remplacez `[self refresh]` dans `viewDidLoad` dans *QSTodoListViewController.m* par le code suivant :

    ```Objective-C
    [self loginAndGetData];
    ```

3. Ouvrez le fichier`QSAppDelegate.h` et ajoutez le code suivant :

    ```Objective-C
    #import "QSTodoService.h"

    @property (strong, nonatomic) QSTodoService *qsTodoService;
    ```

4. Ouvrez le fichier`QSAppDelegate.m` et ajoutez le code suivant :

    ```Objective-C
    - (BOOL)application:(UIApplication *)application openURL:(NSURL *)url options:(NSDictionary<UIApplicationOpenURLOptionsKey,id> *)options
    {
        if ([[url.scheme lowercaseString] isEqualToString:@"appname"]) {
            // Resume login flow
            return [self.qsTodoService.client resumeWithURL:url];
        }
        else {
            return NO;
        }
    }
    ```

   Ajoutez ce code juste avant la lecture de la ligne `#pragma mark - Core Data stack`.  Remplacez la chaîne _appname_ par la valeur de urlScheme utilisée à l’étape 1.

5. Ouvrez le fichier `AppName-Info.plist` (en remplaçant AppName par le nom de votre application) et ajoutez le code suivant :

    ```XML
    <key>CFBundleURLTypes</key>
    <array>
        <dict>
            <key>CFBundleURLName</key>
            <string>com.microsoft.azure.zumo</string>
            <key>CFBundleURLSchemes</key>
            <array>
                <string>appname</string>
            </array>
        </dict>
    </array>
    ```

    Ce code doit être placé dans l’élément `<dict>`.  Remplacez la chaîne _appname_ (dans le tableau pour **CFBundleURLSchemes**) par le nom d’application choisi à l’étape 1.  Vous pouvez également effectuer ces modifications dans l’éditeur plist - cliquez sur le fichier `AppName-Info.plist` dans XCode pour ouvrir l’éditeur plist.

    Remplacez la chaîne `com.microsoft.azure.zumo` pour **CFBundleURLName** par votre identifiant d’offre groupée Apple.

6. Appuyez sur *Exécuter* pour démarrer l’application, puis ouvrez une session. Une fois connecté, vous devez être en mesure d’afficher la liste des tâches et d’effectuer des mises à jour.

**Swift**:

1. Sur votre Mac, ouvrez *ToDoTableViewController.swift* dans Xcode et ajoutez la méthode suivante :

    ```swift
    func loginAndGetData() {

        guard let client = self.table?.client, client.currentUser == nil else {
            return
        }

        let appDelegate = UIApplication.shared.delegate as! AppDelegate
        appDelegate.todoTableViewController = self

        let loginBlock: MSClientLoginBlock = {(user, error) -> Void in
            if (error != nil) {
                print("Error: \(error?.localizedDescription)")
            }
            else {
                client.currentUser = user
                print("User logged in: \(user?.userId)")
            }
        }

        client.login(withProvider:"google", urlScheme: "appname", controller: self, animated: true, completion: loginBlock)

    }
    ```

    Remplacez *google* par *microsoftaccount*, *twitter*, *facebook* ou *windowsazureactivedirectory* si vous n’utilisez pas Google comme fournisseur d’identité. Si vous utilisez Facebook, vous devrez [autoriser les domaines Facebook][1] dans votre application.

    Remplacez **urlScheme** par un nom unique pour votre application.  La chaîne urlScheme doit être identique au protocole de schéma d’URL spécifié dans le champ **URL de redirection externes autorisés** dans le portail Azure. La chaîne urlScheme est utilisée par le rappel d’authentification pour revenir à votre application une fois la demande d’authentification terminée.

2. Supprimez les lignes `self.refreshControl?.beginRefreshing()` et `self.onRefresh(self.refreshControl)` à la fin de `viewDidLoad()` dans *ToDoTableViewController.swift*. Ajoutez un appel à `loginAndGetData()` à leur place :

    ```swift
    loginAndGetData()
    ```

3. Ouvrez le fichier `AppDelegate.swift` et ajoutez la ligne de suivante à la classe `AppDelegate` :

    ```swift
    var todoTableViewController: ToDoTableViewController?

    func application(_ application: UIApplication, openURL url: NSURL, options: [UIApplicationOpenURLOptionsKey : Any] = [:]) -> Bool {
        if url.scheme?.lowercased() == "appname" {
            return (todoTableViewController!.table?.client.resume(with: url as URL))!
        }
        else {
            return false
        }
    }
    ```

    Remplacez la chaîne _appname_ par la valeur de urlScheme utilisée à l’étape 1.

4. Ouvrez le fichier `AppName-Info.plist` (en remplaçant AppName par le nom de votre application) et ajoutez le code suivant :

    ```xml
    <key>CFBundleURLTypes</key>
    <array>
        <dict>
            <key>CFBundleURLName</key>
            <string>com.microsoft.azure.zumo</string>
            <key>CFBundleURLSchemes</key>
            <array>
                <string>appname</string>
            </array>
        </dict>
    </array>
    ```

    Ce code doit être placé dans l’élément `<dict>`.  Remplacez la chaîne _appname_ (dans le tableau pour **CFBundleURLSchemes**) par le nom d’application choisi à l’étape 1.  Vous pouvez également effectuer ces modifications dans l’éditeur plist - cliquez sur le fichier `AppName-Info.plist` dans XCode pour ouvrir l’éditeur plist.

    Remplacez la chaîne `com.microsoft.azure.zumo` pour **CFBundleURLName** par votre identifiant d’offre groupée Apple.

5. Appuyez sur *Exécuter* pour démarrer l’application, puis ouvrez une session. Une fois connecté, vous devez être en mesure d’afficher la liste des tâches et d’effectuer des mises à jour.

L’authentification App Service utilise la communication inter-application d’Apple.  Pour plus d’informations sur ce sujet, reportez-vous à la [Documentation Apple][2]
<!-- URLs. -->

[1]: https://developers.facebook.com/docs/ios/ios9#whitelist
[2]: https://developer.apple.com/library/content/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/Inter-AppCommunication/Inter-AppCommunication.html
[ portail Azure]: https://portal.azure.com

[Démarrage rapide iOS]: app-service-mobile-ios-get-started.md

