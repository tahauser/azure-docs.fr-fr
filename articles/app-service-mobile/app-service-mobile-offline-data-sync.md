---
title: "Synchronisation des données hors connexion dans Azure Mobile Apps | Microsoft Docs"
description: "Référence conceptuelle et présentation de la fonctionnalité de synchronisation des données hors connexion pour Azure Mobile Apps"
documentationcenter: windows
author: conceptdev
manager: crdun
editor: 
services: app-service\mobile
ms.assetid: 982fb683-8884-40da-96e6-77eeca2500e3
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 10/30/2016
ms.author: crdun
ms.openlocfilehash: 5ea1d655f50da49be88f7b6ae91231c4d2258fa7
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2018
---
# <a name="offline-data-sync-in-azure-mobile-apps"></a>Synchronisation des données hors connexion dans Azure Mobile Apps
## <a name="what-is-offline-data-sync"></a>Qu’est-ce que la synchronisation des données hors connexion ?
La synchronisation des données hors connexion est une fonctionnalité du Kit de développement logiciel (SDK) client et serveur d’Azure Mobile Apps qui permet aux développeurs de créer des applications fonctionnant sans connexion réseau.

Quand votre application est en mode hors connexion, vous pouvez toujours créer et modifier des données, qui sont enregistrées dans un magasin local. Quand l’application est de nouveau en ligne, elle peut synchroniser les modifications locales avec le backend d’application Azure Mobile App. La fonctionnalité prend également en charge la détection des conflits quand un même enregistrement est modifié sur le client et le backend. Les conflits peuvent ensuite être traités sur le serveur ou le client.

La synchronisation hors connexion présente plusieurs avantages :

* Améliorer la réactivité de l’application en mettant en cache les données de serveur localement sur l’appareil
* Créer des applications robustes qui demeurent opérationnelles en cas de problèmes réseau
* Permettre aux utilisateurs finaux de créer et de modifier des données même en l’absence d’accès au réseau, prenant ainsi en charge des scénarios avec une connectivité faible ou nulle
* Synchroniser des données sur plusieurs appareils et détecter des conflits lorsque le même enregistrement est modifié par deux appareils
* Limiter l’utilisation du réseau sur les réseaux à forte latence ou limités

Les didacticiels suivants montrent comment ajouter la synchronisation hors connexion à vos clients mobiles à l’aide d’Azure Mobile Apps :

* [Android : activer la synchronisation hors connexion]
* [Apache Cordova : activer la synchronisation hors connexion](app-service-mobile-cordova-get-started-offline-data.md)
* [iOS : activer la synchronisation hors connexion]
* [Xamarin iOS : activer la synchronisation hors connexion]
* [Xamarin Android : activer la synchronisation hors connexion]
* [Xamarin.Forms : activer la synchronisation hors connexion](app-service-mobile-xamarin-forms-get-started-offline-data.md)
* [Plateforme Windows universelle : activer la synchronisation hors connexion]

## <a name="what-is-a-sync-table"></a>Qu’est-ce qu’une table de synchronisation ?
Pour accéder au point de terminaison « /tables », les Kits de développement logiciel (SDK) client Azure Mobile fournissent des interfaces comme `IMobileServiceTable` (SDK client .NET) ou `MSTable` (client iOS). Ces API se connectent directement à l’application principale Azure Mobile App et échouent si l’appareil client n’a pas de connexion réseau.

Pour prendre en charge l’utilisation hors connexion, votre application doit plutôt utiliser les API de *table de synchronisation*, comme `IMobileServiceSyncTable` (SDK client .NET) ou `MSSyncTable` (client iOS). Les mêmes opérations CRUD (Create, Read, Update, Delete) fonctionnent avec les API de table de synchronisation, mais s’exécutent alors par rapport à un *magasin local*. Avant de pouvoir effectuer des opérations de table de synchronisation, le magasin local doit être initialisé.

## <a name="what-is-a-local-store"></a>Qu’est-ce qu’un magasin local ?
Un magasin local est la couche de persistance des données sur l’appareil client. Par défaut, les Kits de développement logiciel (SDK) clients Azure Mobile Apps prennent en charge l’implémentation des magasins locaux. Dans Windows, Xamarin et Android, cette implémentation est basée sur SQLite. Sur iOS, la solution fonctionne sur les données de base.

Pour utiliser l’implémentation basée sur SQLite dans Windows Phone ou Windows Store 8.1, vous devez installer une extension SQLite. Pour plus d’informations, voir [Plateforme Windows universelle : activer la synchronisation hors connexion]. Le système d’exploitation des appareils Android et iOS comportant une version de SQLite, vous n’avez pas besoin de référencer votre propre version de SQLite.

Les développeurs peuvent également implémenter leur propre magasin local. Par exemple, si vous souhaitez stocker des données dans un format chiffré sur le client mobile, vous pouvez définir un magasin local qui utilise SQLCipher pour le chiffrement.

## <a name="what-is-a-sync-context"></a>Qu’est-ce qu’un contexte de synchronisation ?
Un *contexte de synchronisation* est associé à un objet client mobile (comme `IMobileServiceClient` ou `MSClient`) et effectue le suivi des modifications apportées avec les tables de synchronisation. Le contexte de synchronisation gère une *file d’attente d’opérations* qui conserve une liste ordonnée d’opérations CUD (Create, Update, Delete) destinées à être envoyées au serveur.

Un magasin local est associé au contexte de synchronisation à l’aide d’une méthode d’initialisation comme `IMobileServicesSyncContext.InitializeAsync(localstore)` dans le [Kit de développement logiciel (SDK) client .NET].

## <a name="how-sync-works"></a>Fonctionnement de la synchronisation hors connexion
Quand vous utilisez des tables de synchronisation, votre code client détermine à quel moment les modifications locales sont synchronisées avec une application principale Azure Mobile App. Rien n’est envoyé au backend tant que n’a pas été émis un appel pour *envoyer* les modifications locales. De même, le magasin local n’est rempli avec de nouvelles données que si un appel pour *extraire* les données est émis.

* **Envoi**: l’envoi est une opération sur le contexte de synchronisation et consiste à envoyer toutes les modifications CUD qui se sont produites depuis le dernier envoi. Notez qu’il n’est pas possible d’envoyer uniquement les modifications d’une table spécifique, car sinon les opérations pourraient être envoyées dans le désordre. L’envoi exécute une série d’appels REST à destination de votre application principale Azure Mobile App qui, à son tour, modifie votre base de données serveur.
* **Extraction**: l’extraction est effectuée sur une table spécifique et peut être personnalisée avec une requête pour récupérer uniquement un sous-ensemble des données du serveur. Les Kits de développement logiciel (SDK) clients Azure Mobile insèrent ensuite les données résultantes dans le magasin local.
* **Envois implicites**: si une extraction est exécutée sur une table en attente de mises à jour locales, cette extraction commence par exécuter un `push()` sur le contexte de synchronisation. Cela opération Push permet de réduire au minimum les conflits entre les modifications qui sont déjà en attente et les nouvelles données du serveur.
* **Synchronisation incrémentielle**: le premier paramètre de l’opération d’extraction est un *nom de requête* qui est utilisé uniquement sur le client. Si vous utilisez un nom de requête non null, le Kit de développement logiciel (SDK) Azure Mobile effectue une *synchronisation incrémentielle*. Chaque fois qu’une opération d’extraction retourne un jeu de résultats, le dernier horodatage `updatedAt` à partir de ce jeu de résultats est stocké dans les tables de système locales du Kit de développement logiciel (SDK). Les opérations d’extraction ultérieures n’extraient que les enregistrements postérieurs à cet horodatage.

  Pour utiliser la synchronisation incrémentielle, votre serveur doit retourner des valeurs `updatedAt` explicites et prendre en charge le tri par ce champ. Toutefois, étant donné que le Kit de développement logiciel (SDK) ajoute son propre tri sur le champ updatedAt, vous ne pouvez pas utiliser une requête d’extraction qui a sa propre clause `orderBy` .

  Le nom de la requête peut être toute chaîne de votre choix, mais il doit être unique pour chaque requête logique dans votre application.
  Sinon, différentes opérations d’extraction pourraient écraser le même horodatage de synchronisation incrémentielle et vos requêtes pourraient renvoyer des résultats incorrects.

  Si la requête possède un paramètre, une façon de créer un nom de requête unique consiste à intégrer la valeur du paramètre.
  Par exemple, si vous filtrez sur le nom d’utilisateur, le nom de votre requête peut être le suivant (en C#) :

        await todoTable.PullAsync("todoItems" + userid,
            syncTable.Where(u => u.UserId == userid));

  Si vous souhaitez désactiver la synchronisation incrémentielle, transmettez `null` en tant qu'ID de requête. Dans ce cas, tous les enregistrements sont extraits à chaque appel à `PullAsync`, ce qui est potentiellement inefficace.
* **Purge** : vous pouvez effacer le contenu du magasin local en utilisant `IMobileServiceSyncTable.PurgeAsync`.
  Une purge peut s’avérer nécessaire si la base de données client contient des données obsolètes ou que vous souhaitez ignorer toutes les modifications en attente.

  Une purge efface une table du magasin local. Si des opérations sont en attente de synchronisation avec la base de données du serveur, la purge lève une exception, à moins que le paramètre *force purge* soit activé.

  Pour illustrer les données obsolètes sur le client, supposons que dans l’exemple de la liste de tâches, l’appareil 1 extrait uniquement les éléments qui ne sont pas terminés. Un élément de tâche « Acheter du lait » est marqué comme terminé sur le serveur par un autre appareil. Toutefois, l’appareil 1 a toujours l’élément de tâche « Acheter du lait » dans le magasin local, car il extrait uniquement les éléments qui ne sont pas marqués comme terminés. Une purge efface cet élément obsolète.

## <a name="next-steps"></a>Étapes suivantes
* [iOS : activer la synchronisation hors connexion]
* [Xamarin iOS : activer la synchronisation hors connexion]
* [Xamarin Android : activer la synchronisation hors connexion]
* [Plateforme Windows universelle : activer la synchronisation hors connexion]

<!-- Links -->
[Kit de développement logiciel (SDK) client .NET]: app-service-mobile-dotnet-how-to-use-client-library.md
[Android : activer la synchronisation hors connexion]: app-service-mobile-android-get-started-offline-data.md
[iOS : activer la synchronisation hors connexion]: app-service-mobile-ios-get-started-offline-data.md
[Xamarin iOS : activer la synchronisation hors connexion]: app-service-mobile-xamarin-ios-get-started-offline-data.md
[Xamarin Android : activer la synchronisation hors connexion]: app-service-mobile-xamarin-android-get-started-offline-data.md
[Plateforme Windows universelle : activer la synchronisation hors connexion]: app-service-mobile-windows-store-dotnet-get-started-offline-data.md
