---
title: Activation de la synchronisation hors connexion pour votre application Azure Mobile App (Xamarin Android)
description: "Découvrez comment utiliser App Service Mobile App pour mettre en cache et synchroniser des données hors connexion dans votre application Xamarin Android"
documentationcenter: xamarin
author: conceptdev
manager: crdun
editor: 
services: app-service\mobile
ms.assetid: 91d59e4b-abaa-41f4-80cf-ee7933b32568
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: 5c6ff5ac909e2dc6918f85d39beb781952ee6dd0
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2018
---
# <a name="enable-offline-sync-for-your-xamarinandroid-mobile-app"></a>Activation de la synchronisation hors connexion pour votre application mobile Xamarin Android
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Vue d'ensemble
Ce didacticiel présente la fonctionnalité de synchronisation hors connexion des applications mobiles Azure pour Xamarin.Android. La synchronisation hors connexion permet aux utilisateurs finaux d’interagir avec une application mobile pour afficher, ajouter ou modifier des données, même lorsqu’il n’existe aucune connexion réseau. Les modifications sont stockées dans une base de données locale.
Une fois l'appareil de nouveau en ligne, ces modifications sont synchronisées avec le service distant.

Dans ce didacticiel vous allez mettre à jour le projet client du didacticiel [Création d’une application Xamarin Android] pour prendre en charge les fonctionnalités hors connexion d’Azure Mobile Apps. Si vous n’utilisez pas le projet de serveur du démarrage rapide téléchargé, vous devez ajouter les packages d’extension d’accès aux données à votre projet. Pour plus d'informations sur les packages d'extension de serveur, consultez [Fonctionnement avec le Kit de développement logiciel (SDK) du serveur principal .NET pour Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Pour plus d’informations sur la fonctionnalité de synchronisation hors connexion, consultez la rubrique [Synchronisation des données hors connexion dans Azure Mobile Apps].

## <a name="update-the-client-app-to-support-offline-features"></a>Mettre à jour l’application cliente pour prendre en charge les fonctionnalités hors connexion
Les fonctionnalités hors connexion de l’application mobile Azure vous permettent d’interagir avec une base de données locale hors connexion. Pour utiliser ces fonctionnalités dans votre application, vous initialisez un [SyncContext] dans un magasin local. Ensuite, vous référencez votre table par le biais de l’interface [IMobileServiceSyncTable][IMobileServiceSyncTable]. SQLite est utilisé comme magasin local sur l’appareil.

1. Dans Visual Studio, ouvrez le gestionnaire de package NuGet du projet que vous avez finalisé dans le didacticiel [Création d’une application Xamarin Android].  Recherchez et installez le package NuGet **Microsoft.Azure.Mobile.Client.SQLiteStore**.
2. Ouvrez le fichier ToDoActivity.cs et supprimez les marques de commentaire de la définition `#define OFFLINE_SYNC_ENABLED`.
3. Dans Visual Studio, appuyez sur **F5** pour régénérer et exécuter l'application cliente. L’application fonctionne comme elle le faisait avant l’activation de la synchronisation hors connexion. Toutefois, la base de données locale est maintenant remplie avec des données qui peuvent être utilisées dans un scénario hors connexion.

## <a name="update-sync"></a>Mise à jour de l’application pour se déconnecter du backend
Dans cette section, vous rompez la connexion avec votre backend d’application mobile afin de simuler un scénario hors connexion. Lorsque vous ajouterez des éléments de données, votre gestionnaire d’exceptions signalera que l’application est en mode hors connexion. Dans cet état, les éléments ajoutés au magasin local sont synchronisés avec le serveur principal de l’application mobile lorsqu’un envoi est exécuté en état connecté.

1. Modifiez ToDoActivity.cs dans le projet partagé. Modifiez la variable **applicationURL** afin de pointer vers une URL non valide :

         const string applicationURL = @"https://your-service.azurewebsites.fail";

    Vous pouvez également illustrer le comportement en mode hors connexion en désactivant les réseaux Wi-Fi et cellulaire sur l’appareil ou en utilisant le mode avion.
2. Appuyez sur **F5** pour générer et exécuter l'application. Notez que la synchronisation a échoué lors de l’actualisation au lancement de l’application.
3. Entrez de nouveaux éléments et notez que l’opération d’envoi échoue avec un état [CancelledByNetworkError] chaque fois que vous cliquez sur **Enregistrer**. Cependant, les nouveaux éléments todo existent dans le magasin local tant qu’ils ne sont pas transmis au backend d’applications mobiles.  Dans une application de production, si vous supprimez ces exceptions, l’application cliente se comporte comme si elle est encore connectée au backend de l’application mobile.
4. Fermez l’application et redémarrez-la pour vérifier que les nouveaux élément que vous avez créés sont conservés dans le magasin local.
5. (Facultatif) Dans Visual Studio, ouvrez l’ **Explorateur de serveurs**. Accédez à votre base de données dans **Azure**->**Bases de données SQL**. Cliquez avec le bouton droit sur votre base de données, puis sélectionnez **Ouvrir dans l’Explorateur d’objets SQL Server**. Maintenant, vous pouvez accéder à votre table de base de données SQL et à son contenu. Vérifiez que les données dans la base de données backend n’ont pas changé.
6. (Facultatif) Utilisez un outil REST tel que Fiddler ou Postman pour interroger votre serveur principal mobile, à l’aide d’une requête GET au format `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`.

## <a name="update-online-app"></a>Mettre à jour l’application pour la reconnecter à votre backend d’applications mobiles
Dans cette section, reconnectez l’application au serveur principal d’applications mobiles. Lors de la première exécution de l’application, le gestionnaire d’événements `OnCreate` appelle `OnRefreshItemsSelected`. Cette méthode appelle `SyncAsync` pour synchroniser votre magasin local avec la base de données du serveur principal.

1. Ouvrez ToDoActivity.cs dans le projet partagé et annulez votre modification de la propriété **applicationURL**.
2. Appuyez sur **F5** pour régénérer et exécuter l'application. L’application synchronise vos modifications locales avec le serveur principal d’applications mobiles Azure par des opérations d’extraction et d’envoi lorsque la méthode `OnRefreshItemsSelected` s’exécute.
3. (Facultatif) Affichez les données mises à jour à l’aide de l’Explorateur d’objets SQL Server ou d’un outil REST tel que Fiddler. Notez que les données ont été synchronisées entre la base de données du backend d’application mobile Azure et le magasin local.
4. Dans l'application, cochez la case en regard de quelques éléments pour les exécuter dans le magasin local.

   `CheckItem` appelle `SyncAsync` pour synchroniser chaque élément complété avec le backend d’application mobile. `SyncAsync` appelle des opérations Push et Pull. **Chaque fois que vous exécutez une opération d’extraction sur une table modifiée par le client, une opération d’envoi préalable est effectuée automatiquement**. Cela garantit la cohérence de toutes les tables dans le magasin local, mais aussi des relations. Ce comportement peut entraîner un envoi inattendu. Pour plus d’informations, consultez la page [Synchronisation des données hors connexion dans Azure Mobile Apps].

## <a name="review-the-client-sync-code"></a>Examiner le code de synchronisation client
Le projet client Xamarin que vous avez téléchargé pendant l’exécution du didacticiel [Création d’une application Xamarin Android] contient déjà du code prenant en charge la synchronisation hors connexion à l’aide d’une base de données SQLite locale. Voici un bref aperçu de ce qui est déjà inclus dans le code du didacticiel. Pour obtenir une vue d’ensemble conceptuelle de la fonctionnalité, consultez [Synchronisation des données hors connexion dans Azure Mobile Apps].

* Avant de pouvoir effectuer des opérations de table, le magasin local doit être initialisé. La base de données du magasin local est initialisée quand `ToDoActivity.OnCreate()` exécute `ToDoActivity.InitLocalStoreAsync()`. Cette méthode crée une base de données SQLite locale à l’aide de la classe `MobileServiceSQLiteStore` fournie par le Kit de développement logiciel (SDK) client Azure Mobile Apps.

    La méthode `DefineTable` crée une table dans le magasin local qui correspond aux champs dans le type fourni, `ToDoItem` ici. Ce type n’a pas besoin d’inclure toutes les colonnes qui se trouvent dans la base de données distante. Il est possible de stocker uniquement un sous-ensemble de colonnes.

        // ToDoActivity.cs
        private async Task InitLocalStoreAsync()
        {
            // new code to initialize the SQLite store
            string path = Path.Combine(System.Environment
                .GetFolderPath(System.Environment.SpecialFolder.Personal), localDbFilename);

            if (!File.Exists(path))
            {
                File.Create(path).Dispose();
            }

            var store = new MobileServiceSQLiteStore(path);
            store.DefineTable<ToDoItem>();

            // Uses the default conflict handler, which fails on conflict
            // To use a different conflict handler, pass a parameter to InitializeAsync.
            // For more details, see http://go.microsoft.com/fwlink/?LinkId=521416.
            await client.SyncContext.InitializeAsync(store);
        }
* Le membre `toDoTable` de `ToDoActivity` est du type `IMobileServiceSyncTable` au lieu d’`IMobileServiceTable`. Ainsi, IMobileServiceSyncTable redirige toutes les opérations de table CRUD (Create, Read, Update et Delete) vers la base de données du magasin local.

    Vous décidez à quel moment ces modifications sont envoyées au serveur principal d’applications mobiles Azure en appelant `IMobileServiceSyncContext.PushAsync()`. Le contexte de synchronisation permet de conserver les relations entre les tables grâce au suivi et à l’envoi des modifications dans toutes les tables qu’une application cliente modifie quand `PushAsync` est appelé.

    Le code fourni appelle `ToDoActivity.SyncAsync()` pour effectuer une synchronisation chaque fois que la liste des éléments de tâche est actualisée ou qu’un élément de tâche est ajouté ou terminé. Le code se synchronise après chaque modification locale.

    Dans le code fourni, tous les enregistrements de la table `TodoItem` distante sont interrogés, mais il est également possible de filtrer les enregistrements en transmettant un ID de requête et une requête à `PushAsync`. Pour plus d’informations, consultez la section *Synchronisation incrémentielle* dans [Synchronisation des données hors connexion dans Azure Mobile Apps].

        // ToDoActivity.cs
        private async Task SyncAsync()
        {
            try {
                await client.SyncContext.PushAsync();
                await toDoTable.PullAsync("allTodoItems", toDoTable.CreateQuery()); // query ID is used for incremental sync
            } catch (Java.Net.MalformedURLException) {
                CreateAndShowDialog (new Exception ("There was an error creating the Mobile Service. Verify the URL"), "Error");
            } catch (Exception e) {
                CreateAndShowDialog (e, "Error");
            }
        }

## <a name="additional-resources"></a>Ressources supplémentaires
* [Synchronisation des données hors connexion dans Azure Mobile Apps]
* [SDK HOWTO Azure Mobile Apps .NET][8]

<!-- URLs. -->
[Création d’une application Xamarin Android]: ../app-service-mobile-xamarin-android-get-started.md
[Synchronisation des données hors connexion dans Azure Mobile Apps]: ../app-service-mobile-offline-data-sync.md

<!-- Images -->

<!-- URLs. -->
[Création d’une application Xamarin Android]: app-service-mobile-xamarin-android-get-started.md
[Synchronisation des données hors connexion dans Azure Mobile Apps]: app-service-mobile-offline-data-sync.md
[Xamarin Studio]: http://xamarin.com/download
[Xamarin extension]: http://xamarin.com/visual-studio
[SyncContext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
