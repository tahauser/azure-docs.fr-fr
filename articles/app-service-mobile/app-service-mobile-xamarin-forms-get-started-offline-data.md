---
title: Activation de la synchronisation hors connexion pour votre application Azure Mobile App (Xamarin.Forms) | Microsoft Docs
description: "Découvrez comment utiliser App Service Mobile App pour mettre en cache et synchroniser des données hors connexion dans votre application Xamarin.Forms"
documentationcenter: xamarin
author: conceptdev
manager: yochayk
editor: 
services: app-service\mobile
ms.assetid: acf0f874-3ea5-4410-bd22-b0e72140f3b5
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 10/04/2016
ms.author: crdun
ms.openlocfilehash: f88e6a4037bcca54982359742cdc6021f020882d
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2018
---
# <a name="enable-offline-sync-for-your-xamarinforms-mobile-app"></a>Activer la synchronisation hors connexion pour votre application mobile Xamarin.Forms
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Vue d'ensemble
Ce didacticiel présente la fonctionnalité de synchronisation hors connexion d’Azure Mobile Apps pour Xamarin.Forms. La synchronisation hors connexion permet aux utilisateurs finaux d’interagir avec une application mobile pour afficher, ajouter ou modifier des données, même lorsqu’il n’existe aucune connexion réseau. Les modifications sont stockées dans une base de données locale. Une fois l'appareil de nouveau en ligne, ces modifications sont synchronisées avec le service distant.

Ce didacticiel est basé sur la solution de démarrage rapide de Xamarin.Forms pour les applications mobiles créées en suivant le didacticiel [Création d’une application Xamarin iO]. La solution de démarrage rapide de Xamarin.Forms contient le code de prise en charge de la synchronisation hors connexion, qui doit simplement être activé. Dans ce didacticiel, vous mettez à jour la solution de démarrage rapide pour activer les fonctionnalités hors connexion d’Azure Mobile Apps. Vous en découvrez également plus sur le code hors connexion spécifique dans l’application. Si vous n’utilisez pas la solution de démarrage rapide téléchargée, vous devez ajouter les packages d’extension d’accès aux données à votre projet. Pour plus d'informations sur les packages d'extension de serveur, consultez [Utiliser le kit SDK du serveur backend .NET pour Azure Mobile Apps][1].

Pour en savoir plus sur la fonctionnalité de synchronisation hors connexion, consultez la rubrique [Synchronisation des données hors connexion dans Azure Mobile Apps][2].

## <a name="enable-offline-sync-functionality-in-the-quickstart-solution"></a>Activer la fonctionnalité de synchronisation hors connexion dans la solution de démarrage rapide
Le code de synchronisation hors connexion est inclus dans le projet à l’aide de directives de préprocesseur C#. Lorsque le symbole **OFFLINE\_SYNC\_ENABLED** est défini, ces chemins de code sont inclus dans la build. Pour les applications Windows, vous devez également installer la plateforme SQLite.

1. Dans Visual Studio, cliquez sur la solution > **Gérer les packages NuGet pour la solution...**, puis recherchez et installez le package NuGet **Microsoft.Azure.Mobile.Client.SQLiteStore** pour tous les projets dans la solution.
2. Dans l’Explorateur de solutions, ouvrez le fichier TodoItemManager.cs du projet comportant **Portable** dans son nom (projet de bibliothèque de classes portables), puis supprimez les marques de commentaire de la directive de préprocesseur suivante :

        #define OFFLINE_SYNC_ENABLED
3. (Facultatif) Pour prendre en charge des appareils Windows, installez l’un des packages runtime SQLite suivants :

   * **Windows 8.1 Runtime :** installez [SQLite pour Windows 8.1][3].
   * **Windows Phone 8.1 :** installez [SQLite pour Windows Phone 8.1][4].
   * **Plateforme Windows universelle** : installez [SQLite pour plateforme Windows universelle][5].

     Bien que le démarrage rapide ne contienne pas un projet Windows universel, la plateforme Windows universelle est prise en charge par Xamarin.Forms.
4. (Facultatif) Dans chaque projet d’application Windows, cliquez avec le bouton droit sur **Références** > **Ajouter une référence...**, développez le dossier **Windows** > **Extensions**.
    Activer le Kit de développement logiciel (SDK) **SQLite for Windows** approprié ainsi que le SDK **Visual C++ 2013 Runtime for Windows**.
    Les noms de SDK SQLite varient légèrement en fonction de la plateforme Windows utilisée.

## <a name="review-the-client-sync-code"></a>Examiner le code de synchronisation client
Voici un bref aperçu de ce qui est déjà inclus dans le code du didacticiel au sein des directives `#if OFFLINE_SYNC_ENABLED` . La fonctionnalité de synchronisation hors connexion se trouve dans le fichier TodoItemManager.cs du projet de bibliothèque de classes portables. Pour obtenir une vue d’ensemble conceptuelle de la fonctionnalité, consultez [Synchronisation des données hors connexion dans Azure Mobile Apps][2].

* Avant de pouvoir effectuer des opérations de table, le magasin local doit être initialisé. La base de données du magasin local est initialisée dans le constructeur de classe **TodoItemManager** en utilisant le code suivant :

        var store = new MobileServiceSQLiteStore(OfflineDbPath);
        store.DefineTable<TodoItem>();

        //Initializes the SyncContext using the default IMobileServiceSyncHandler.
        this.client.SyncContext.InitializeAsync(store);

        this.todoTable = client.GetSyncTable<TodoItem>();

    Ce code crée une nouvelle base de données SQLite locale à l’aide de la classe **MobileServiceSQLiteStore**.

    La méthode **DefineTable** crée une table dans le magasin local qui correspond aux champs dans le type fourni.  Ce type n’a pas besoin d’inclure toutes les colonnes qui se trouvent dans la base de données distante. Il est possible de stocker uniquement un sous-ensemble de colonnes.
* Le champ **todoTable** dans **TodoItemManager** est un type **IMobileServiceSyncTable** au lieu du type **IMobileServiceTable**. Cette classe utilise la base de données locale pour toutes les opérations de table CRUD (Create, Read, Update et Delete). Vous décidez à quel moment ces modifications sont envoyées au backend Mobile App en appelant **PushAsync** sur le type **IMobileServiceSyncContext**. Le contexte de synchronisation permet de conserver les relations entre les tables grâce au suivi et à l’envoi des modifications dans toutes les tables qu’une application cliente modifie quand **PushAsync** est appelé.

    La méthode **SyncAsync** suivante est appelée pour la synchronisation avec le backend Mobile App :

        public async Task SyncAsync()
        {
            ReadOnlyCollection<MobileServiceTableOperationError> syncErrors = null;

            try
            {
                await this.client.SyncContext.PushAsync();

                await this.todoTable.PullAsync(
                    "allTodoItems",
                    this.todoTable.CreateQuery());
            }
            catch (MobileServicePushFailedException exc)
            {
                if (exc.PushResult != null)
                {
                    syncErrors = exc.PushResult.Errors;
                }
            }

            // Simple error/conflict handling.
            if (syncErrors != null)
            {
                foreach (var error in syncErrors)
                {
                    if (error.OperationKind == MobileServiceTableOperationKind.Update && error.Result != null)
                    {
                        //Update failed, reverting to server's copy.
                        await error.CancelAndUpdateItemAsync(error.Result);
                    }
                    else
                    {
                        // Discard local change.
                        await error.CancelAndDiscardItemAsync();
                    }

                    Debug.WriteLine(@"Error executing sync operation. Item: {0} ({1}). Operation discarded.",
                        error.TableName, error.Item["id"]);
                }
            }
        }

    Cet exemple utilise la gestion simple des erreurs avec le gestionnaire de synchronisation par défaut. Une application réelle gèrerait les différentes erreurs telles que les conditions du réseau et les conflits de serveur à l’aide d’une implémentation personnalisée de **IMobileServiceSyncHandler**.

## <a name="offline-sync-considerations"></a>Considérations relatives à la synchronisation hors connexion
Dans l’exemple, la méthode **SyncAsync** est appelée uniquement au démarrage et lorsqu’une synchronisation est demandée.  Pour lancer la synchronisation dans une application Android ou iOS, extrayez la liste des éléments ; pour Windows, utilisez le bouton **Synchronisation**. Dans une application réelle, vous pourriez également provoquer le déclenchement de la synchronisation lorsque l’état du réseau change.

Si une extraction est exécutée sur une table qui comporte des mises à jour locales en attente faisant l’objet d’un suivi en fonction du contexte, cette opération déclenche automatiquement un envoi préalable sur le contexte. Lors de l’actualisation, de l’ajout et de l’achèvement des éléments dans cet exemple, vous pouvez omettre l’appel explicite de **PushAsync**.

Dans le code fourni, tous les enregistrements de la table TodoItem distante sont interrogés, mais il est également possible de filtrer les enregistrements en transmettant un ID de requête et une requête à **PushAsync**. Pour plus d’informations, consultez la section *Synchronisation incrémentielle* dans [Synchronisation des données hors connexion dans Azure Mobile Apps][2].

## <a name="run-the-client-app"></a>Exécuter l’application cliente
Maintenant que la synchronisation hors connexion est activée, exécutez l’application cliente au moins une fois sur chaque plateforme pour remplir la base de données du magasin local. Ensuite, simulez un scénario hors connexion et modifiez les données du magasin local pendant que l’application est hors connexion.

## <a name="update-the-sync-behavior-of-the-client-app"></a>Mettre à jour le comportement de synchronisation de l’application cliente
Dans cette section, modifiez le projet client pour simuler un scénario hors connexion à l’aide d’une URL d’application non valide pour votre backend. Sinon, vous pouvez désactiver les connexions réseau en basculant votre appareil sur « Mode avion ».  Quand vous ajoutez ou modifiez des éléments de données, ces modifications sont conservées dans le magasin local, mais ne sont synchronisées avec le magasin de données backend qu’une fois la connexion rétablie.

1. Dans l’Explorateur de solutions, ouvrez le fichier Constants.cs dans le projet **Portable** et changez la valeur de `ApplicationURL` pour pointer vers une URL non valide :

        public static string ApplicationURL = @"https://your-service.azurewebsites.net/";
2. Ouvrez le fichier TodoItemManager.cs du projet **Portable**, puis ajoutez un **catch** pour la classe **Exception**de base au bloc **try... catch** dans **SyncAsync**. Ce bloc **catch** écrit le message d’exception dans la console, comme suit :

            catch (Exception ex)
            {
                Console.Error.WriteLine(@"Exception: {0}", ex.Message);
            }
3. Générez et exécutez l’application cliente.  Ajoutez de nouveaux éléments. Notez l’exception consignée dans la console pour chaque tentative de synchronisation avec le backend. Ces nouveaux éléments se trouvent uniquement dans le magasin local jusqu’à ce qu’ils puissent être transmis par push vers le backend mobile. L’application cliente se comporte comme si elle était connectée au backend et prend en charge toutes les opérations de création, lecture, mise à jour et suppression.
4. Fermez l'application et redémarrez-la pour vérifier que les nouveaux élément que vous avez créés sont conservés dans le magasin local.
5. (Facultatif) À l’aide de Visual Studio, affichez votre table Azure SQL Database pour constater que les données dans la base de données backend n’ont pas changé.

    Dans Visual Studio, ouvrez l' **Explorateur de serveurs**. Accédez à votre base de données dans **Azure**->**Bases de données SQL**. Cliquez avec le bouton droit sur votre base de données, puis sélectionnez **Ouvrir dans l’Explorateur d’objets SQL Server**. Maintenant, vous pouvez accéder à votre table de base de données SQL et à son contenu.

## <a name="update-the-client-app-to-reconnect-your-mobile-backend"></a>Mettre à jour l’application cliente pour reconnecter votre backend mobile
Dans cette section, reconnectez l'application au backend mobile, ce qui simule le retour de l'application à un état en ligne. Quand vous effectuez le mouvement d'actualisation, les données sont synchronisées avec votre backend mobile.

1. Rouvrez Constants.cs. Corrigez le paramètre `applicationURL` de manière à ce qu’il pointe vers l’URL correcte.
2. Régénérez et exécutez l’application cliente. Après son lancement, l’application tente une synchronisation avec le backend Mobile App. Vérifiez qu’aucune exception n’est consignée dans la console de débogage.
3. (Facultatif) Affichez les données mises à jour à l’aide de l’Explorateur d’objets SQL Server ou d’un outil REST tel que Fiddler ou [Postman][6]. Notez que les données n’ont pas été synchronisées entre la base de données backend et le magasin local.

    Notez que les données ont été synchronisées entre la base de données et le magasin local et qu’elles contiennent les éléments que vous avez ajoutés pendant que votre application était déconnectée.

## <a name="additional-resources"></a>Ressources supplémentaires
* [Synchronisation des données hors connexion dans Azure Mobile Apps][2]
* [SDK HOWTO Azure Mobile Apps .NET][8]

<!-- URLs. -->
[1]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[2]: app-service-mobile-offline-data-sync.md
[3]: http://go.microsoft.com/fwlink/p/?LinkID=716919
[4]: http://go.microsoft.com/fwlink/p/?LinkID=716920
[5]: http://sqlite.org/2016/sqlite-uwp-3120200.vsix
[6]: https://www.getpostman.com/
[7]: http://www.telerik.com/fiddler
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
