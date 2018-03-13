---
title: Activation de la synchronisation hors connexion pour votre application Azure Mobile App (Cordova) | Microsoft Docs
description: "Découvrez comment utiliser App Service Mobile App pour mettre en cache et synchroniser des données hors connexion dans votre application Cordova"
documentationcenter: cordova
author: conceptdev
manager: crdun
editor: 
services: app-service\mobile
ms.assetid: 1a3f685d-f79d-4f8b-ae11-ff96e79e9de9
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-cordova-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 10/30/2016
ms.author: crdun
ms.openlocfilehash: c12328a441a8cc438fa3e974863cc8adf8651b50
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2018
---
# <a name="enable-offline-sync-for-your-cordova-mobile-app"></a>Activation de la synchronisation hors connexion pour votre application mobile Cordova
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

Ce didacticiel présente la fonctionnalité de synchronisation hors connexion d’Azure Mobile Apps pour Cordova. La synchronisation hors connexion permet aux utilisateurs finaux d’interagir avec une application mobile &mdash;pour afficher, ajouter ou modifier des données&mdash;, même lorsqu’il n’existe aucune connexion réseau. Les modifications sont stockées dans une base de données locale.  Une fois l'appareil de nouveau en ligne, ces modifications sont synchronisées avec le service distant.

Ce didacticiel est basé sur la solution de démarrage rapide de Cordova pour les applications mobiles créées en suivant le didacticiel [Démarrage rapide Apache Cordova]. Dans ce didacticiel, vous allez mettre à jour la solution de démarrage rapide pour ajouter les fonctionnalités hors connexion d’Azure Mobile Apps.  Vous en découvrez également plus sur le code hors connexion spécifique dans l’application.

Pour plus d’informations sur la fonctionnalité de synchronisation hors connexion, consultez la rubrique [Synchronisation des données hors connexion dans Azure Mobile Apps]. Pour plus d’informations d’utilisation de l’API, consultez la [documentation de l’API](https://azure.github.io/azure-mobile-apps-js-client).

## <a name="add-offline-sync-to-the-quickstart-solution"></a>Ajouter la synchronisation hors connexion à la solution de démarrage rapide
Le code de synchronisation hors connexion doit être ajouté à l’application. La synchronisation hors connexion requiert le plug-in cordova-sqlite-storage, qui est automatiquement ajouté à votre application lorsque le plug-in Azure Mobile Apps est inclus dans le projet. Le projet de démarrage rapide inclut ces deux plug-ins.

1. Dans l’Explorateur de solutions de Visual Studio, ouvrez le fichier index.js et remplacez le code suivant

        var client,            // Connection to the Azure Mobile App backend
           todoItemTable;      // Reference to a table endpoint on backend

    par ce code :

        var client,            // Connection to the Azure Mobile App backend
           todoItemTable,      // Reference to a table endpoint on backend
           syncContext;        // Reference to offline data sync context

2. Ensuite, remplacez le code suivant :

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net');

    par ce code :

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net');
        var store = new WindowsAzure.MobileServiceSqliteStore('store.db');

        store.defineTable({
          name: 'todoitem',
          columnDefinitions: {
              id: 'string',
              text: 'string',
              complete: 'boolean',
              version: 'string'
          }
        });

        // Get the sync context from the client
        syncContext = client.getSyncContext();

    Les ajouts de code précédents initialisent le magasin local et définissent une table locale correspondant aux valeurs de colonne utilisées dans votre serveur principal Azure. (Vous n’avez pas besoin d’inclure toutes les valeurs de colonne dans ce code.)  Le champ `version` est géré par le serveur principal mobile et est utilisé pour la résolution des conflits.

    Vous obtenez une référence au contexte de synchronisation en appelant **getSyncContext**. Le contexte de synchronisation permet de conserver les relations entre les tables grâce au suivi et à l’envoi des modifications dans toutes les tables qu’une application cliente modifie quand `.push()` est appelé.

3. Mettez à jour l’URL de l’application pour l’URL de votre application Mobile App.

4. Ensuite, remplacez ce code :

        todoItemTable = client.getTable('todoitem'); // todoitem is the table name

    par ce code :

        // Initialize the sync context with the store
        syncContext.initialize(store).then(function () {

        // Get the local table reference.
        todoItemTable = client.getSyncTable('todoitem');

        syncContext.pushHandler = {
            onConflict: function (pushError) {
                // Handle the conflict.
                console.log("Sync conflict! " + pushError.getError().message);
                // Update failed, revert to server's copy.
                pushError.cancelAndDiscard();
              },
              onError: function (pushError) {
                  // Handle the error
                  // In the simulated offline state, you get "Sync error! Unexpected connection failure."
                  console.log("Sync error! " + pushError.getError().message);
              }
        };

        // Call a function to perform the actual sync
        syncBackend();

        // Refresh the todoItems
        refreshDisplay();

        // Wire up the UI Event Handler for the Add Item
        $('#add-item').submit(addItemHandler);
        $('#refresh').on('click', refreshDisplay);

    Le code précédent initialise le contexte de synchronisation, puis appelle getSyncTable (au lieu de getTable) pour obtenir une référence à la table locale.

    Ce code utilise la base de données locale pour toutes les opérations de table CRUD (Create, Read, Update et Delete - Créer, Lire, Mettre à jour et Supprimer).

    Cet exemple effectue une simple gestion des erreurs sur les conflits de synchronisation. Une application réelle gèrerait les différentes erreurs telles que les conditions du réseau, les conflits de serveur et autres. Pour plus d’exemples de code, consultez l’ [l’exemple de synchronisation hors connexion].

5. Ensuite, ajoutez cette fonction pour effectuer la synchronisation réelle.

        function syncBackend() {

          // Sync local store to Azure table when app loads, or when login complete.
          syncContext.push().then(function () {
              // Push completed

          });

          // Pull items from the Azure table after syncing to Azure.
          syncContext.pull(new WindowsAzure.Query('todoitem'));
        }

    Vous décidez du moment auquel envoyer les modifications au serveur principal Mobile App en appelant **syncContext.push()**. Par exemple, vous pouvez appeler **syncBackend** dans un gestionnaire d’événement de bouton lié à un bouton de synchronisation.

## <a name="offline-sync-considerations"></a>Considérations relatives à la synchronisation hors connexion

Dans l’exemple, la méthode **push** de **syncContext** n’est appelée qu’au démarrage de l’application dans la fonction de rappel pour la connexion.  Dans une application réelle, vous pourriez également provoquer manuellement le déclenchement de la fonctionnalité de synchronisation lorsque l’état du réseau change.

Si une extraction est exécutée sur une table qui comporte des mises à jour locales en attente faisant l’objet d’un suivi en fonction du contexte, cette opération déclenche automatiquement un envoi. Lors de l’actualisation, de l’ajout et de l’achèvement des éléments dans cet exemple, vous pouvez omettre l’appel explicite de **push**, puisqu’il peut être redondant.

Dans le code fourni, tous les enregistrements de la table TodoItem distante sont interrogés, mais il est également possible de filtrer les enregistrements en transmettant un ID de requête et une requête à **push**. Pour plus d’informations, consultez la section *Synchronisation incrémentielle* dans [Synchronisation des données hors connexion dans Azure Mobile Apps].

## <a name="optional-disable-authentication"></a>(Facultatif) Désactiver l’authentification

Si vous ne souhaitez pas le faire avant de tester la synchronisation hors connexion, commentez la fonction de rappel pour la connexion, mais pas le code à l’intérieur de la fonction de rappel.  Après avoir commenté les lignes de connexion, le code suit :

      // Login to the service.
      // client.login('twitter')
      //    .then(function () {
        syncContext.initialize(store).then(function () {
          // Leave the rest of the code in this callback function  uncommented.
                ...
        });
      // }, handleError);

À présent, l’application se synchronise avec le serveur principal Azure lorsque vous exécutez l’application.

## <a name="run-the-client-app"></a>Exécuter l’application cliente
Maintenant que la synchronisation hors connexion est activée, vous pouvez exécuter l’application cliente au moins une fois sur chaque plateforme pour remplir la base de données du magasin local. Ensuite, simulez un scénario hors connexion et modifiez les données du magasin local pendant que l’application est hors connexion.

## <a name="optional-test-the-sync-behavior"></a>(Facultatif) Tester le comportement de synchronisation
Dans cette section, vous modifiez le projet client pour simuler un scénario hors connexion à l’aide d’une URL d’application non valide pour votre serveur principal. Quand vous ajoutez ou modifiez des éléments de données, ces modifications sont conservées dans le magasin local, mais ne sont synchronisées avec le magasin de données principal qu’une fois la connexion rétablie.

1. Dans l’Explorateur de solutions, ouvrez le fichier de projet index.js et changez l’URL de l’application pour pointer vers une URL non valide, comme dans le code suivant :

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net-fail');

2. Dans index.html, mettez à jour l’élément CSP `<meta>` avec la même URL non valide.

        <meta http-equiv="Content-Security-Policy" content="default-src 'self' data: gap: http://yourmobileapp.azurewebsites.net-fail; style-src 'self'; media-src *">

3. Créez et exécutez l’application cliente et notez qu’une exception est consignée dans la console lorsque l’application tente de se synchroniser avec le backend après la connexion. Les nouveaux éléments que vous ajoutez se trouvent uniquement dans le magasin local jusqu’à ce qu’ils puissent être transmis par push vers le serveur principal mobile. L’application cliente se comporte comme si elle était connectée au serveur principal.

4. Fermez l’application et redémarrez-la pour vérifier que les nouveaux élément que vous avez créés sont conservés dans le magasin local.

5. (Facultatif) À l’aide de Visual Studio, affichez votre table Azure SQL Database pour constater que les données dans la base de données backend n’ont pas changé.

    Dans Visual Studio, ouvrez l' **Explorateur de serveurs**. Accédez à votre base de données dans **Azure**->**Bases de données SQL**. Cliquez avec le bouton droit sur votre base de données, puis sélectionnez **Ouvrir dans l’Explorateur d’objets SQL Server**. Maintenant, vous pouvez accéder à votre table de base de données SQL et à son contenu.

## <a name="optional-test-the-reconnection-to-your-mobile-backend"></a>(Facultatif) Tester la reconnexion à votre backend

Dans cette section, vous reconnectez l'application au serveur principal mobile, ce qui simule le retour de l'application à un état en ligne. Lorsque vous vous connectez, les données sont synchronisées avec votre serveur principal mobile.

1. Rouvrez index.js et restaurez l’URL de l’application.
2. Rouvrez le fichier index.html et corrigez l’URL de l’application dans l’élément CSP `<meta>` .
3. Régénérez et exécutez l’application cliente. Après la connexion, l’application tente de se synchroniser avec le backend Mobile App. Vérifiez qu’aucune exception n’est consignée dans la console de débogage.
4. (Facultatif) Affichez les données mises à jour à l’aide de l’Explorateur d’objets SQL Server ou d’un outil REST tel que Fiddler. Notez que les données n’ont pas été synchronisées entre la base de données du serveur principal et le magasin local.

    Notez que les données ont été synchronisées entre la base de données et le magasin local et qu’elles contiennent les éléments que vous avez ajoutés pendant que votre application était déconnectée.

## <a name="additional-resources"></a>Ressources supplémentaires
* [Synchronisation des données hors connexion dans Azure Mobile Apps]
* [Visual Studio Tools pour Apache Cordova]

## <a name="next-steps"></a>Étapes suivantes
* Consultez les fonctionnalités de synchronisation hors connexion plus avancées, comme la résolution des conflits dans [l’exemple de synchronisation hors connexion]
* Consultez la référence de l’API de synchronisation hors connexion dans la [documentation de l’API](https://azure.github.io/azure-mobile-apps-js-client).

<!-- ##Summary -->

<!-- Images -->

<!-- URLs. -->
[Démarrage rapide Apache Cordova]: app-service-mobile-cordova-get-started.md
[l’exemple de synchronisation hors connexion]: https://github.com/Azure-Samples/app-service-mobile-cordova-client-conflict-handling
[Synchronisation des données hors connexion dans Azure Mobile Apps]: app-service-mobile-offline-data-sync.md
[Cloud Cover: Offline Sync in Azure Mobile Services]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Adding Authentication]: app-service-mobile-cordova-get-started-users.md
[authentication]: app-service-mobile-cordova-get-started-users.md
[Work with the .NET backend server SDK for Azure Mobile Apps]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Visual Studio Community 2015]: http://www.visualstudio.com/
[Visual Studio Tools pour Apache Cordova]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
[Apache Cordova SDK]: app-service-mobile-cordova-how-to-use-client-library.md
[ASP.NET Server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Node.js Server SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md
