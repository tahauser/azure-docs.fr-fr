---
title: Activer la synchronisation hors connexion avec des applications mobiles iOS | Microsoft Docs
description: "Découvrez comment utiliser les applications mobiles Azure App Service pour mettre en cache et synchroniser des données hors connexion dans des applications iOS."
documentationcenter: ios
author: conceptdev
manager: crdun
editor: 
services: app-service\mobile
ms.assetid: eb5b9520-0f39-4a09-940a-dadb6d940db8
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: b676b51241e4883fb1b4c40caba8e281bfa68a4c
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2018
---
# <a name="enable-offline-syncing-with-ios-mobile-apps"></a>Activer la synchronisation hors connexion avec des applications mobiles iOS
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Vue d'ensemble
Ce didacticiel traite de la synchronisation hors connexion à l’aide de la fonctionnalité Mobile Apps d’Azure App Service pour iOS. La synchronisation hors connexion permet aux utilisateurs finaux d’interagir avec une application mobile pour afficher, ajouter ou modifier des données, même en l’absence d’une connexion réseau. Les modifications sont stockées dans une base de données locale. Quand l’appareil est de nouveau en ligne, les modifications sont synchronisées avec le backend distant.

Si c’est la première fois que vous utilisez Mobile Apps, commencez par suivre le didacticiel [Créer une application iOS]. Si vous n’utilisez pas le projet de serveur du démarrage rapide téléchargé, vous devez ajouter les packages d’extension d’accès aux données à votre projet. Pour plus d'informations sur les packages d'extension de serveur, consultez [Fonctionnement avec le Kit de développement logiciel (SDK) du serveur principal .NET pour Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Pour en savoir plus sur la fonctionnalité de synchronisation hors connexion, consultez [Synchronisation des données hors connexion dans Mobile Apps].

## <a name="review-sync"></a>Examiner le code de synchronisation client
Le projet client que vous avez téléchargé pour le didacticiel [Créer une application iOS] contient déjà du code prenant en charge la synchronisation hors connexion à l’aide d’une base de données Core Data locale. Cette section résume ce qui est déjà inclus dans le code du didacticiel. Pour obtenir une vue d’ensemble conceptuelle de la fonctionnalité, consultez [Synchronisation des données hors connexion dans Mobile Apps].

La fonctionnalité de synchronisation des données hors connexion de Mobile Apps permet aux utilisateurs d’interagir avec une base de données locale quand le réseau n’est pas accessible. Pour pouvoir utiliser ces fonctionnalités dans votre application, vous devez initialiser le contexte de synchronisation de `MSClient` et référencer un magasin local. Ensuite, référencez votre table par le biais de l’interface **MSSyncTable**.

Dans **QSTodoService.m** (Objective-C) ou **ToDoTableViewController.swift** (Swift), notez que le type du membre **syncTable** est **MSSyncTable**. La synchronisation hors connexion utilise cette interface de table de synchronisation à la place de **MSTable**. Quand une table de synchronisation est utilisée, toutes les opérations vont vers le magasin local et sont uniquement synchronisées avec le backend distant à l’aide d’opérations push et pull explicites.

 Pour obtenir une référence à une table de synchronisation, utilisez la méthode **syncTableWithName** sur `MSClient`. Pour supprimer la fonctionnalité de synchronisation hors connexion, utilisez plutôt **tableWithName**.

Avant de pouvoir effectuer des opérations de table, le magasin local doit être initialisé. Voici le code correspondant :

* **Objective-C**. Dans la méthode **QSTodoService.init** :

   ```objc
   MSCoreDataStore *store = [[MSCoreDataStore alloc] initWithManagedObjectContext:context];
   self.client.syncContext = [[MSSyncContext alloc] initWithDelegate:nil dataSource:store callback:nil];
   ```    
* **Swift**. Dans la méthode **ToDoTableViewController.viewDidLoad** :

   ```swift
   let client = MSClient(applicationURLString: "http:// ...") // URI of the Mobile App
   let managedObjectContext = (UIApplication.sharedApplication().delegate as! AppDelegate).managedObjectContext!
   self.store = MSCoreDataStore(managedObjectContext: managedObjectContext)
   client.syncContext = MSSyncContext(delegate: nil, dataSource: self.store, callback: nil)
   ```
   Cette méthode crée un magasin local à l’aide de l’interface `MSCoreDataStore` fournie dans le SDK Mobile Apps. Vous pouvez également fournir un autre magasin local en implémentant le protocole `MSSyncContextDataSource`. Par ailleurs, le premier paramètre de **MSSyncContext** est utilisé pour spécifier un gestionnaire de conflits. Comme nous avons passé `nil`, nous obtenons le gestionnaire de conflits par défaut, qui échoue en cas de conflit.

Nous allons maintenant exécuter l’opération de synchronisation réelle et obtenir des données à partir du backend distant :

* **Objective-C**. `syncData` envoie d’abord par push des nouvelles modifications, puis appelle **pullData** pour obtenir des données du backend distant. À son tour, la méthode **pullData** obtient de nouvelles données qui correspondent à une requête :

   ```objc
   -(void)syncData:(QSCompletionBlock)completion
   {
       // Push all changes in the sync context, and then pull new data.
       [self.client.syncContext pushWithCompletion:^(NSError *error) {
           [self logErrorIfNotNil:error];
           [self pullData:completion];
       }];
   }

   -(void)pullData:(QSCompletionBlock)completion
   {
       MSQuery *query = [self.syncTable query];

       // Pulls data from the remote server into the local table.
       // We're pulling all items and filtering in the view.
       // Query ID is used for incremental sync.
       [self.syncTable pullWithQuery:query queryId:@"allTodoItems" completion:^(NSError *error) {
           [self logErrorIfNotNil:error];

           // Lets the caller know that we have finished.
           if (completion != nil) {
               dispatch_async(dispatch_get_main_queue(), completion);
           }
       }];
   }
   ```
* **Swift**:
   ```swift
   func onRefresh(sender: UIRefreshControl!) {
      UIApplication.sharedApplication().networkActivityIndicatorVisible = true

      self.table!.pullWithQuery(self.table?.query(), queryId: "AllRecords") {
          (error) -> Void in

          UIApplication.sharedApplication().networkActivityIndicatorVisible = false

          if error != nil {
              // A real application would handle various errors like network conditions,
              // server conflicts, etc via the MSSyncContextDelegate
              print("Error: \(error!.description)")

              // We will discard our changes and keep the server's copy for simplicity
              if let opErrors = error!.userInfo[MSErrorPushResultKey] as? Array<MSTableOperationError> {
                  for opError in opErrors {
                      print("Attempted operation to item \(opError.itemId)")
                      if (opError.operation == .Insert || opError.operation == .Delete) {
                          print("Insert/Delete, failed discarding changes")
                          opError.cancelOperationAndDiscardItemWithCompletion(nil)
                      } else {
                          print("Update failed, reverting to server's copy")
                          opError.cancelOperationAndUpdateItem(opError.serverItem!, completion: nil)
                      }
                  }
              }
          }
          self.refreshControl?.endRefreshing()
      }
   }
   ```

Dans la version Objective-C, dans `syncData`, nous appelons d’abord **pushWithCompletion** sur le contexte de synchronisation. Cette méthode est membre de `MSSyncContext` (plutôt que la table de synchronisation), car elle envoie par push des modifications à toutes les tables. Seuls les enregistrements qui ont été modifiés au niveau local (par le biais d’opérations CUD) sont envoyés au serveur. L’application d’assistance **pullData** est ensuite appelée. Celle-ci appelle **MSSyncTable.pullWithQuery** pour récupérer les données distantes et les stocker dans la base de données locale.

Étant donné que l’opération push n’est pas strictement nécessaire dans la version Swift, **pushWithCompletion** n’est pas appelé. S'il existe des modifications en attente dans le contexte de synchronisation pour la table qui effectue une opération push, pull émet toujours d'abord un push. Mais si vous avez plus d’une table de synchronisation, il est préférable d’appeler explicitement push pour vous assurer que tout est cohérent dans les tables associées.

Dans les versions Objective-C et Swift, la méthode **pullWithQuery** vous permet de spécifier une requête pour filtrer les enregistrements à récupérer. Dans cet exemple, la requête récupère tous les enregistrements de la table `TodoItem` distante.

Le deuxième paramètre de **pullWithQuery** est un ID de requête utilisé pour la *synchronisation incrémentielle*. La synchronisation incrémentielle récupère uniquement les enregistrements modifiés depuis la dernière synchronisation, à l’aide de l’horodateur `UpdatedAt` de l’enregistrement (appelé `updatedAt` dans le magasin local). L’ID de requête doit être une chaîne descriptive unique pour chaque requête logique de votre application. Pour refuser la synchronisation incrémentielle, passez `nil` comme ID de requête. Cette approche peut ne pas être très efficace, car elle récupère tous les enregistrements de chaque opération d’extraction.

L’application Objective-C se synchronise quand vous modifiez ou ajoutez des données, quand un utilisateur effectue le mouvement d’actualisation et au lancement.

L’application Swift se synchronise quand l’utilisateur effectue le mouvement d’actualisation et au lancement.

Comme l’application se synchronise quand les données sont modifiées (Objective-C) ou à chaque démarrage de l’application (Objective-C et Swift), l’application suppose que l’utilisateur est en ligne. Dans une section ultérieure, vous mettrez à jour l’application pour que les utilisateurs puissent les modifier même s’ils sont hors connexion.

## <a name="review-core-data"></a>Examen du modèle de données de base
Quand vous utilisez le magasin hors connexion Core Data, vous devez définir certaines tables et certains champs dans le modèle de données. L'exemple d'application comprend déjà un modèle de données avec le format correct. Dans cette section, nous allons passer en revue ces tables pour voir comment elles sont utilisées.

Ouvrez **QSDataModel.xcdatamodeld**. Quatre tables sont définies : trois sont utilisées par le SDK et la dernière est utilisée pour les éléments de tâche :
  * MS_TableOperations : effectue le suivi des éléments à synchroniser avec le serveur.
  * MS_TableOperationErrors : effectue le suivi des erreurs qui se produisent pendant la synchronisation hors connexion.
  * MS_TableConfig : effectue le suivi de l’heure de la dernière mise à jour de la dernière opération de synchronisation pour toutes les opérations d’extraction.
  * TodoItem : stocke les éléments de tâche. Les colonnes système **createdAt**, **updatedAt** et **version** sont des propriétés système facultatives.

> [!NOTE]
> Le SDK Mobile Apps réserve les noms de colonnes qui commencent par « **``** ». N’utilisez ce préfixe qu’avec les colonnes système. Sinon, vos noms de colonnes sont modifiés quand vous utilisez le backend distant.
>
>

Quand vous utilisez la fonctionnalité de synchronisation hors connexion, définissez les trois tables système et la table de données.

### <a name="system-tables"></a>Tables système

**MS_TableOperations**  

![Attributs de table MS_TableOperations][defining-core-data-tableoperations-entity]

| Attribut | type |
| --- | --- |
| id | Integer 64 |
| itemId | Chaîne |
| properties | Binary Data |
| table | Chaîne |
| tableKind | Integer 16 |


**MS_TableOperationErrors**

 ![Attributs de table MS_TableOperationErrors][defining-core-data-tableoperationerrors-entity]

| Attribut | type |
| --- | --- |
| id |Chaîne |
| operationId |Integer 64 |
| properties |Binary Data |
| tableKind |Integer 16 |

 **MS_TableConfig**

 ![][defining-core-data-tableconfig-entity]

| Attribut | type |
| --- | --- |
| id |Chaîne |
| key |Chaîne |
| keyType |Integer 64 |
| table |Chaîne |
| value |Chaîne |

### <a name="data-table"></a>Table de données

**TodoItem**

| Attribut | type | Remarque |
| --- | --- | --- |
| id | Chaîne, marquée requise |Clé primaire dans le magasin distant |
| terminé | Booléen | Champ d’élément de tâche |
| text |Chaîne |Champ d’élément de tâche |
| createdAt | Date | (facultatif) Correspond à la propriété système **createdAt** |
| updatedAt | Date | (facultatif) Correspond à la propriété système **updatedAt** |
| version | Chaîne | (facultatif) Permet de détecter les conflits, correspond à version |

## <a name="setup-sync"></a>Modification du comportement de synchronisation de l’application
Dans cette section, vous modifiez l’application pour qu’elle ne se synchronise pas au démarrage de l’application ou quand vous insérez et mettez à jour des éléments. Elle se synchronise uniquement quand le mouvement d’actualisation est effectué.

**Objective-C**:

1. Dans **QSTodoListViewController.m**, modifiez la méthode **viewDidLoad** pour supprimer l’appel à `[self refresh]` à la fin de la méthode. Désormais, les données ne sont pas synchronisées avec le serveur au démarrage de l’application. Au lieu de cela, elles sont synchronisées avec le contenu du magasin local.
2. Dans **QSTodoService.m**, modifiez la définition de `addItem` afin que la synchronisation ne se fasse qu’une fois l’élément inséré. Supprimez le bloc `self syncData` et remplacez-le par ce qui suit :

   ```objc
   if (completion != nil) {
       dispatch_async(dispatch_get_main_queue(), completion);
   }
   ```
3. Modifiez la définition de `completeItem` comme indiqué précédemment. Supprimez le bloc pour `self syncData` et remplacez-le par ce qui suit :
   ```objc
   if (completion != nil) {
       dispatch_async(dispatch_get_main_queue(), completion);
   }
   ```

**Swift**:

Dans `viewDidLoad`, dans **ToDoTableViewController.swift**, commentez les deux lignes indiquées ici pour arrêter la synchronisation au démarrage de l’application. Au moment de la rédaction de cet article, l’application Swift Todo ne met pas à jour le service quand un utilisateur ajoute ou termine un élément. Elle met à jour le service uniquement au démarrage de l’application.

   ```swift
  self.refreshControl?.beginRefreshing()
  self.onRefresh(self.refreshControl)
```

## <a name="test-app"></a>Test de l’application
Dans cette section, vous allez vous connecter à une URL incorrecte pour simuler un scénario hors connexion. Quand vous ajoutez des éléments de données, ceux-ci sont placés dans le magasin Core Data local, mais ils ne sont pas synchronisés avec le backend de l’application mobile.

1. Remplacez l’URL de l’application mobile dans **QSTodoService.m** par une URL non valide, puis réexécutez l’application :

   **Objective-C**. Dans QSTodoService.m :
   ```objc
   self.client = [MSClient clientWithApplicationURLString:@"https://sitename.azurewebsites.net.fail"];
   ```
   **Swift**. Dans ToDoTableViewController.swift :
   ```swift
   let client = MSClient(applicationURLString: "https://sitename.azurewebsites.net.fail")
   ```
2. Ajoutez des éléments de tâche. Quittez le simulateur (ou forcez la fermeture de l’application), puis redémarrez-le. Vérifiez que vos modifications sont persistantes.

3. Affichez le contenu de la table **TodoItem** distante :
   * Pour un backend Node.js, accédez au [portail Azure](https://portal.azure.com/), puis dans le backend de l’application mobile, cliquez sur **Tables faciles** > **TodoItem**.  
   * Pour un backend .NET, utilisez un outil SQL, comme SQL Server Management Studio, ou un client REST, comme Fiddler ou Postman.  

4. Vérifiez que les nouveaux éléments *n’ont pas* été synchronisés avec le serveur.

5. Remplacez l’URL par l’URL correcte dans **QSTodoService.m** et réexécutez l’application.

6. Effectuez l’actualisation en faisant glisser la liste des éléments vers le bas.  
Un compteur de progression s’affiche.

7. Affichez de nouveau les données **TodoItem**. Les nouveaux élément de tâche et ceux qui ont été modifiés doivent maintenant s’afficher.

## <a name="summary"></a>Résumé
Pour prendre en charge la fonctionnalité de synchronisation hors connexion, nous avons utilisé l’interface `MSSyncTable` et initialisé `MSClient.syncContext` avec un magasin local. Dans ce cas, le magasin local était une base de données basée sur Core Data.

Quand vous utilisez un magasin local Core Data, vous devez définir certaines tables avec les [propriétés système correctes](#review-core-data).

Les opérations normales de création, lecture, mise à jour et suppression pour les applications mobiles fonctionnent comme si l’application était toujours connectée, mais toutes les opérations se rapportent au magasin local.

Pour synchroniser le magasin local avec le serveur, nous avons utilisé la méthode **MSSyncTable.pullWithQuery**.

## <a name="additional-resources"></a>Ressources supplémentaires
* [Synchronisation des données hors connexion dans Mobile Apps]
* [Cloud Cover : synchronisation hors connexion dans Azure Mobile Services]\( La vidéo traite de Mobile Services, mais la synchronisation hors connexion Mobile Apps fonctionne de façon similaire.\)

<!-- URLs. -->


[Créer une application iOS]: app-service-mobile-ios-get-started.md
[Synchronisation des données hors connexion dans Mobile Apps]: app-service-mobile-offline-data-sync.md

[defining-core-data-tableoperationerrors-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperationerrors-entity.png
[defining-core-data-tableoperations-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperations-entity.png
[defining-core-data-tableconfig-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableconfig-entity.png
[defining-core-data-todoitem-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-todoitem-entity.png

[Cloud Cover : synchronisation hors connexion dans Azure Mobile Services]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Offline-enabled apps in Azure Mobile Services]: http://azure.microsoft.com/en-us/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/
