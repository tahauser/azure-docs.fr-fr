---
title: Activer la synchronisation hors connexion pour votre application de plateforme Windows universelle (UWP) avec Mobile Apps| Microsoft Docs
description: "Découvrez comment utiliser une application Azure Mobile pour mettre en cache et synchroniser les données hors connexion dans votre application de plateforme Windows universelle (UWP)."
documentationcenter: windows
author: conceptdev
manager: crdun
editor: 
services: app-service\mobile
ms.assetid: 8fe51773-90de-4014-8a38-41544446d9b5
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: a16de4cef82c29f9b6becfae1901662ee1936934
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2018
---
# <a name="enable-offline-sync-for-your-windows-app"></a>Activation de la synchronisation hors connexion pour votre application Windows
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Vue d'ensemble
Ce didacticiel explique comment prendre en charge le mode hors connexion dans une application de plateforme Windows universelle (UWP) à l’aide d’un backend d’applications mobiles Azure. La synchronisation hors connexion permet aux utilisateurs finaux d’interagir avec une application mobile pour afficher, ajouter ou modifier des données, même lorsqu’il n’existe aucune connexion réseau. Les modifications sont stockées dans une base de données locale. Une fois l'appareil de nouveau en ligne, ces modifications sont synchronisées avec le serveur principal distant.

Dans ce didacticiel, vous allez mettre à jour le projet d’application UWP du didacticiel [Créer une application Windows] pour prendre en charge les fonctionnalités hors connexion d’Azure Mobile Apps. Si vous n’utilisez pas le projet de serveur du démarrage rapide téléchargé, vous devez ajouter les packages d’extension d’accès aux données à votre projet. Pour plus d'informations sur les packages d'extension de serveur, consultez [Fonctionnement avec le Kit de développement logiciel (SDK) du serveur principal .NET pour Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Pour plus d’informations sur la fonctionnalité de synchronisation hors connexion, consultez la rubrique [Synchronisation des données hors connexion dans Azure Mobile Apps].

## <a name="requirements"></a>Configuration requise
Ce didacticiel nécessite les prérequis suivants :

* Visual Studio 2013 s’exécutant sous Windows 8.1 ou version ultérieure.
* finalisation de [create a windows app][create a windows app].
* [Azure Mobile Services SQLite Store][sqlite store nuget]
* [SQLite pour développement sur plateforme Windows universelle](http://www.sqlite.org/downloads)

## <a name="update-the-client-app-to-support-offline-features"></a>Mettre à jour l’application cliente pour prendre en charge les fonctionnalités hors connexion
Les fonctionnalités hors connexion de l’application mobile Azure vous permettent d’interagir avec une base de données locale hors connexion. Pour utiliser ces fonctionnalités dans votre application, vous initialisez un [SyncContext][synccontext] dans un magasin local. Ensuite, vous référencez votre table par le biais de l'interface [IMobileServiceSyncTable][IMobileServiceSyncTable] . SQLite est utilisé comme magasin local sur l’appareil.

1. Installez le [Runtime SQLite pour la plateforme Windows universelle](http://sqlite.org/2016/sqlite-uwp-3120200.vsix).
2. Dans Visual Studio, ouvrez le gestionnaire de package NuGet du projet d’application UWP que vous avez finalisé dans le didacticiel [Créer une application Windows].
    Recherchez et installez le package NuGet **Microsoft.Azure.Mobile.Client.SQLiteStore**.
3. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur **Références** > **Ajouter une référence...** >**Windows universel**>**Extensions**, puis activez **SQLite pour plateforme Windows universelle** et **Runtime Visual C++ 2015 pour applications pour plateforme Windows universelle**.

    ![Ajouter une référence SQLite UWP][1]
4. Ouvrez le fichier MainPage.xaml.cs et supprimez les marques de commentaires de la définition `#define OFFLINE_SYNC_ENABLED`.
5. Dans Visual Studio, appuyez sur **F5** pour régénérer et exécuter l'application cliente. L’application fonctionne comme elle le faisait avant l’activation de la synchronisation hors connexion. Toutefois, la base de données locale est maintenant remplie avec des données qui peuvent être utilisées dans un scénario hors connexion.

## <a name="update-sync"></a>Mise à jour de l’application pour se déconnecter du backend
Dans cette section, vous rompez la connexion avec votre backend d’application mobile afin de simuler un scénario hors connexion. Lorsque vous ajouterez des éléments de données, votre gestionnaire d’exceptions signalera que l’application est en mode hors connexion. Dans cet état, les nouveaux éléments ajoutés au magasin local seront synchronisés avec le backend de l’application mobile lors de la prochaine opération Push en état connecté.

1. Modifiez App.xaml.cs dans le projet partagé. Mettez en commentaire l’initialisation de **MobileServiceClient** et ajoutez les lignes suivantes qui utilisent une URL d’application mobile incorrecte :

         public static MobileServiceClient MobileService = new MobileServiceClient("https://your-service.azurewebsites.fail");

    Vous pouvez également illustrer le comportement en mode hors connexion en désactivant les réseaux Wi-Fi et cellulaire sur l’appareil ou utiliser le mode avion.
2. Appuyez sur **F5** pour générer et exécuter l'application. Notez que la synchronisation a échoué lors de l’actualisation au lancement de l’application.
3. Entrez de nouveaux éléments et notez que l’opération Push échoue avec un état [CancelledByNetworkError] chaque fois que vous cliquez sur **Enregistrer**. Cependant, les nouveaux éléments todo existent dans le magasin local tant qu’ils ne sont pas transmis au backend d’application mobile.  Dans une application de production, si vous supprimez ces exceptions, l’application cliente se comporte comme si elle est encore connectée au backend de l’application mobile.
4. Fermez l’application et redémarrez-la pour vérifier que les nouveaux élément que vous avez créés sont conservés dans le magasin local.
5. (Facultatif) Dans Visual Studio, ouvrez l’ **Explorateur de serveurs**. Accédez à votre base de données dans **Azure**->**Bases de données SQL**. Cliquez avec le bouton droit sur votre base de données, puis sélectionnez **Ouvrir dans l’Explorateur d’objets SQL Server**. Maintenant, vous pouvez accéder à votre table de base de données SQL et à son contenu. Vérifiez que les données dans la base de données backend n’ont pas changé.
6. (Facultatif) Utilisez un outil REST tel que Fiddler ou Postman pour interroger votre serveur principal mobile, à l’aide d’une requête GET au format `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`.

## <a name="update-online-app"></a>Mettre à jour l’application pour la reconnecter à votre backend d’application mobile
Dans cette section, vous reconnectez l’application au backend d’application mobile. Ces modifications simulent une reconnexion du réseau sur l’application.

Lors de la première exécution de l’application, le gestionnaire d’événements `OnNavigatedTo` appelle `InitLocalStoreAsync`. Cette méthode appelle ensuite `SyncAsync` pour permettre la synchronisation de votre magasin local avec la base de données du backend. L’application fait une tentative de synchronisation au démarrage.

1. Ouvrez App.xaml.cs dans le projet partagé, et supprimez les commentaires de votre précédente initialisation de `MobileServiceClient` pour utiliser la bonne URL de l’application mobile.
2. Appuyez sur **F5** pour régénérer et exécuter l'application. L’application synchronise vos modifications locales sur le backend d’application mobile Azure par des opérations Push et Pull lorsque le gestionnaire d’événements `OnNavigatedTo` s’exécute.
3. (Facultatif) Affichez les données mises à jour à l’aide de l’Explorateur d’objets SQL Server ou d’un outil REST tel que Fiddler. Notez que les données ont été synchronisées entre la base de données du backend d’application mobile Azure et le magasin local.
4. Dans l'application, cochez la case en regard de quelques éléments pour les exécuter dans le magasin local.

   `UpdateCheckedTodoItem` appelle `SyncAsync` pour synchroniser chaque élément complété avec le serveur principal d’applications mobiles. `SyncAsync` appelle des opérations Push et Pull. Toutefois, **chaque fois que vous exécutez une opération Pull sur une table modifiée par le client, une opération push préalable est effectuée automatiquement**. Ce comportement garantit la cohérence de toutes les tables dans le magasin local, mais aussi des relations. Il peut entraîner un push inattendu.  Pour plus d’informations, consultez la page [Synchronisation des données hors connexion dans Azure Mobile Apps].

## <a name="api-summary"></a>Résumé de l’API
Pour pouvoir prendre en charge les fonctionnalités hors connexion des services mobiles, nous avons utilisé l’interface [IMobileServiceSyncTable] et initialisé [MobileServiceClient.SyncContext][synccontext] avec une base de données SQL locale. Lorsque vous êtes hors connexion, Les opérations normales de création, lecture, mise à jour et suppression pour Mobile Apps fonctionnent comme si l’application était toujours connectée, mais toutes les opérations se rapportent au magasin local. Les méthodes suivantes servent à synchroniser le magasin local avec le serveur :

* **[PushAsync]** Cette méthode étant membre de [IMobileServicesSyncContext], les modifications sur toutes les tables sont envoyées au backend par push. Seuls les enregistrements avec des modifications locales sont envoyés au serveur.
* **[PullAsync]** Une extraction (pull) est démarrée à partir d’une interface [IMobileServiceSyncTable]. Lorsque les modifications sont suivies dans une table, une opération Push implicite est exécutée pour s’assurer de la cohérence de toutes les tables du magasin local et des relations. Le paramètre *pushOtherTables* contrôle si d’autres tables dans le contexte sont envoyées par une opération Push implicite. Le paramètre *query* prend une chaîne de requête [IMobileServiceTableQuery<T>][IMobileServiceTableQuery] ou OData pour filtrer les données renvoyées. Le paramètre *queryId* est utilisé pour définir la synchronisation incrémentielle. Pour en savoir plus, consultez [Synchronisation des données hors connexion dans Azure Mobile Apps](app-service-mobile-offline-data-sync.md#how-sync-works).
* **[PurgeAsync]** Votre application doit appeler régulièrement cette méthode pour vider les données obsolètes du magasin local. Utilisez le paramètre *force* lorsque vous avez besoin de vider toutes les modifications qui n’ont pas encore été synchronisées.

Pour plus d’informations sur ces concepts, consultez la page [Synchronisation des données hors connexion dans Azure Mobile Apps](app-service-mobile-offline-data-sync.md#how-sync-works).

## <a name="more-info"></a>En savoir plus
Les rubriques suivantes fournissent des informations générales supplémentaires sur la fonctionnalité de synchronisation hors connexion de Mobile Apps :

* [Synchronisation des données hors connexion dans Azure Mobile Apps]
* [SDK HOWTO Azure Mobile Apps .NET][8]

<!-- Anchors. -->
[Update the app to support offline features]: #enable-offline-app
[Update the sync behavior of the app]: #update-sync
[Update the app to reconnect your Mobile Apps backend]: #update-online-app
[Next Steps]:#next-steps

<!-- Images -->
[1]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/app-service-mobile-add-reference-sqlite-dialog.png
[11]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/app-service-mobile-add-wp81-reference-sqlite-dialog.png
[13]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/cpu-architecture.png


<!-- URLs. -->
[Synchronisation des données hors connexion dans Azure Mobile Apps]: app-service-mobile-offline-data-sync.md
[create a windows app]: app-service-mobile-windows-store-dotnet-get-started.md
[SQLite for Windows 8.1]: http://go.microsoft.com/fwlink/?LinkID=716919
[SQLite for Windows Phone 8.1]: http://go.microsoft.com/fwlink/?LinkID=716920
[SQLite for Windows 10]: http://go.microsoft.com/fwlink/?LinkID=716921
[synccontext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[sqlite store nuget]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client.SQLiteStore/
[IMobileServiceSyncTable]: https://msdn.microsoft.com/library/azure/mt691742(v=azure.10).aspx
[IMobileServiceTableQuery]: https://msdn.microsoft.com/library/azure/dn250631(v=azure.10).aspx
[IMobileServicesSyncContext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.imobileservicesynccontext(v=azure.10).aspx
[MobileServicePushFailedException]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushfailedexception(v=azure.10).aspx
[Status]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushcompletionresult.status(v=azure.10).aspx
[CancelledByNetworkError]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushstatus(v=azure.10).aspx
[PullAsync]: https://msdn.microsoft.com/library/azure/mt667558(v=azure.10).aspx
[PushAsync]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileservicesynccontextextensions.pushasync(v=azure.10).aspx
[PurgeAsync]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.imobileservicesynctable.purgeasync(v=azure.10).aspx
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
