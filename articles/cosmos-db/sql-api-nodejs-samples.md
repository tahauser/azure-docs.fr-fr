---
title: Exemples Node.js pour Azure Cosmos DB | Microsoft Docs
description: Recherchez des exemples Node.js sur GitHub pour les tâches courantes dans Azure Cosmos DB, y compris les opérations CRUD.
keywords: Exemples Node.js
services: cosmos-db
author: moderakh
manager: jhubbard
editor: monicar
documentationcenter: nodejs
ms.assetid: d87d97be-47a5-4928-8d46-a541fbb33213
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2017
ms.author: moderakh
ms.openlocfilehash: 2c9e623c5e99834bba6cc6ccd34e7c6993e1dc94
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="azure-cosmos-db-nodejs-examples"></a>Exemples Node.js pour Azure Cosmos DB
> [!div class="op_single_selector"]
> * [Exemples .NET](sql-api-dotnet-samples.md)
> * [Exemples Node.js](sql-api-nodejs-samples.md)
> * [Exemples Python](sql-api-python-samples.md)
> * [Galerie d’exemples de code Azure](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

Le référentiel GitHub [azure-documentdb-nodejs](https://github.com/Azure/azure-documentdb-node/tree/master/samples) contient des exemples de solutions qui exécutent des opérations CRUD ainsi que d’autres opérations courantes sur les ressources Azure Cosmos DB. Cet article fournit :

* Des liens vers les tâches dans chacun des exemples de fichiers de projet Node.js.
* Des liens vers le contenu de référence d’API connexe.

**Composants requis**

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- Vous pouvez [activer les avantages de votre abonnement Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): ce dernier vous donne droit chaque mois à des crédits dont vous pouvez vous servir pour les services Azure payants.

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

Vous devez également disposer du [Kit de développement logiciel (SDK) Node.js](sql-api-sdk-node.md).
   
   > [!NOTE]
   > Chaque exemple est autonome, se définit lui-même et se nettoie automatiquement. Les exemples transmettent donc plusieurs appels à [DocumentClient.createCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html#createCollection). À chaque appel, votre abonnement sera facturé pour 1 heure d’utilisation selon le niveau de performances de la collection en cours de création.
   > 
   > 

## <a name="database-examples"></a>Exemples de base de données
Le fichier [app.js](https://github.com/Azure/azure-documentdb-node/blob/master/samples/DatabaseManagement/app.js) du projet [DatabaseManagement](https://github.com/Azure/azure-documentdb-node/tree/master/samples/DatabaseManagement) explique comment effectuer les tâches suivantes.

| Tâche | Informations de référence sur l'API |
| --- | --- |
| [Créer une base de données](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DatabaseManagement/app.js#L121-L131) |[DocumentClient.createDatabase](http://azure.github.io/azure-documentdb-node/DocumentClient.html#createDatabase) |
| [Demander un compte pour une base de données](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DatabaseManagement/app.js#L146-L171) |[DocumentClient.queryDatabases](http://azure.github.io/azure-documentdb-node/DocumentClient.html#queryDatabases) |
| [Lire une base de données par identifiant](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DatabaseManagement/app.js#L89-L99) |[DocumentClient.readDatabase](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readDatabase) |
| [Répertorier les bases de données pour un compte](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DatabaseManagement/app.js#L111-L119) |[DocumentClient.readDatabases](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readDatabases) |
| [Supprimer une base de données](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DatabaseManagement/app.js#L133-L144) |[DocumentClient.deleteDatabase](http://azure.github.io/azure-documentdb-node/DocumentClient.html#deleteDatabase) |

## <a name="collection-examples"></a>Exemples de collection
Le fichier [app.js](https://github.com/Azure/azure-documentdb-node/blob/master/samples/CollectionManagement/app.js) du projet [CollectionManagement](https://github.com/Azure/azure-documentdb-node/tree/master/samples/CollectionManagement) explique comment effectuer les tâches suivantes.

| Tâche | Informations de référence sur l'API |
| --- | --- |
| [Création d'une collection](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L97-L118) |[DocumentClient.createCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html#createCollection) |
| [Lire une liste de toutes les collections d’une base de données](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L120-L130) |[DocumentClient.readCollections](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readCollections) |
| [Obtenir une collection par _self](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L132-L141) |[DocumentClient.readCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readCollection) |
| [Obtenir une collection par identifiant](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L143-L156) |[DocumentClient.readCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readCollection) |
| [Obtenir le niveau de performances d’une collection](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L158-L186) |[DocumentQueryable.queryOffers](http://azure.github.io/azure-documentdb-node/DocumentClient.html#queryOffers) |
| [Modifier le niveau de performances d’une collection](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L188-L202) |[DocumentClient.replaceOffer](http://azure.github.io/azure-documentdb-node/DocumentClient.html#replaceOffer) |
| [Supprimer une collection](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.CollectionManagement/app.js#L204-L215) |[DocumentClient.deleteCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html#deleteCollection) |

## <a name="document-examples"></a>Exemples de document
Le fichier [app.js](https://github.com/Azure/azure-documentdb-node/blob/master/samples/DocumentManagement/app.js) du projet [DocumentManagement](https://github.com/Azure/azure-documentdb-node/tree/master/samples/DocumentManagement) explique comment effectuer les tâches suivantes.

| Tâche | Informations de référence sur l'API |
| --- | --- |
| [Création de documents](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L153-L177) |[DocumentClient.createDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#createDocument) |
| [Lire le flux de documents pour une collection](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L179-L189) |[DocumentClient.readDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readDocument) |
| [Lire un document par identifiant](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L191-L201) |[DocumentClient.readDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readDocument) |
| [Lire le document uniquement s’il a changé](https://github.com/Azure/azure-documentdb-node/blob/0778eadea7abb2af41e8c22a239dc872c584f421/samples/DocumentManagement/app.js#L79-L107) |[DocumentClient.readDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#readDocument)<br/>[RequestOptions.accessCondition](http://azure.github.io/azure-documentdb-node/global.html#RequestOptions) |
| [Rechercher des documents](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L82-L110) |[DocumentClient.queryDocuments](http://azure.github.io/azure-documentdb-node/DocumentClient.html#queryDocuments) |
| [Remplacer un document](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L112-L119) |[DocumentClient.replaceDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#replaceDocument) |
| [Remplacer le document par la vérification conditionnelle ETag](https://github.com/Azure/azure-documentdb-node/blob/0778eadea7abb2af41e8c22a239dc872c584f421/samples/DocumentManagement/app.js#L147-L164) |[DocumentClient.replaceDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#replaceDocument)<br/>[RequestOptions.accessCondition](http://azure.github.io/azure-documentdb-node/global.html#RequestOptions) |
| [Supprimer un document](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.DocumentManagement/app.js#L122-L133) |[DocumentClient.deleteDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#deleteDocument) |

## <a name="indexing-examples"></a>Exemples d'indexation
Le fichier [app.js](https://github.com/Azure/azure-documentdb-node/blob/master/samples/IndexManagement/app.js) du projet [IndexManagement](https://github.com/Azure/azure-documentdb-node/tree/master/samples/IndexManagement) explique comment effectuer les tâches suivantes.

| Tâche | Informations de référence sur l'API |
| --- | --- |
| [Créer une collection avec l’indexation par défaut](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L657-L701) |[DocumentClient.createCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html#createCollection) |
| [Indexer manuellement un document spécifique](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L185-L238) |[RequestOptions.indexingDirective: 'include'](http://azure.github.io/azure-documentdb-node/global.html#RequestOptions) |
| [Exclure manuellement un document spécifique de l’index](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L120-L183) |[RequestOptions.indexingDirective: 'exclude'](http://azure.github.io/azure-documentdb-node/global.html#RequestOptions) |
| [Utiliser l’indexation en différé pour l’importation en bloc ou lire des collections volumineuses](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L240-L269) |[IndexingMode.Lazy](http://azure.github.io/azure-documentdb-node/global.html#IndexingMode) |
| [Inclure les chemins d’accès spécifiques d’un document dans l’indexation](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L433-L444) |[IndexingPolicy.IncludedPaths](http://azure.github.io/azure-documentdb-node/global.html#IndexingPolicy) |
| [Exclure certains chemins d’accès de l’indexation](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L427-L450) |[IndexingPolicy.ExcludedPath](http://azure.github.io/azure-documentdb-node/global.html#IndexingPolicy) |
| [Autoriser une analyse sur un chemin d’accès de la chaîne pendant une opération de plage](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L271-L347) |[FeedOptions.EnableScanInQuery](http://azure.github.io/azure-documentdb-node/global.html#FeedOptions) |
| [Créer un index de plage sur un chemin d’accès de la chaîne](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L349-L425) |[IndexKind.Range](http://azure.github.io/azure-documentdb-node/global.html#IndexKind), [IndexingPolicy](http://azure.github.io/azure-documentdb-node/global.html#IndexingPolicy), [DocumentClient.queryDocument](http://azure.github.io/azure-documentdb-node/DocumentClient.html#queryDocument) |
| [Créer une collection avec la valeur par défaut indexPolicy, puis la mettre à jour en ligne](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.IndexManagement/app.js#L519-L614) |[DocumentClient.createCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html#createCollection)<br> [DocumentClient.replaceCollection#replaceCollection](http://azure.github.io/azure-documentdb-node/DocumentClient.html) |

Pour plus d’informations sur l’indexation, consultez [Stratégies d’indexation d’Azure Cosmos DB](indexing-policies.md).

## <a name="server-side-programming-examples"></a>Exemples de programmation côté serveur
Le fichier [app.js](https://github.com/Azure/azure-documentdb-node/blob/master/samples/ServerSideScripts/app.js) du projet [ServerSideScripts](https://github.com/Azure/azure-documentdb-node/tree/master/samples/ServerSideScripts) explique comment effectuer les tâches suivantes.

| Tâche | Informations de référence sur l'API |
| --- | --- |
| [Créer une procédure stockée](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.ServerSideScripts/app.js#L44-L71) |[DocumentClient.createStoredProcedure](http://azure.github.io/azure-documentdb-node/DocumentClient.html#createStoredProcedure) |
| [Exécuter une procédure stockée](https://github.com/Azure/azure-documentdb-node/blob/ef53e5f6707a5dc45920fb6ad54d9c7e008a6c18/samples/DocumentDB.Samples.ServerSideScripts/app.js#L73-L90) |[DocumentClient.executeStoredProcedure](http://azure.github.io/azure-documentdb-node/DocumentClient.html#executeStoredProcedure) |

Pour plus d’informations sur la programmation côté serveur, consultez [Programmation Azure Cosmos DB côté serveur : procédures stockées, déclencheurs de base de données et fonctions définies par l’utilisateur](programming.md).

## <a name="partitioning-examples"></a>Exemples de partitionnement
Le fichier [app.js](https://github.com/Azure/azure-documentdb-node/blob/master/samples/Partitioning/app.js) du projet [Partitioning](https://github.com/Azure/azure-documentdb-node/tree/master/samples/Partitioning) explique comment effectuer les tâches suivantes.

| Tâche | Informations de référence sur l'API |
| --- | --- |
| [Utiliser un HashPartitionResolver](https://github.com/Azure/azure-documentdb-node/blob/ce0fc3c4e70b0279091a1e03620a668d93a14fc2/samples/Partitioning/app.js#L53-L103) |[HashPartitionResolver](http://azure.github.io/azure-documentdb-node/HashPartitionResolver.html) |

Pour en savoir plus sur le partitionnement des données dans Azure Cosmos DB, consultez [Partitionnement, clés de partition et mise à l’échelle dans Azure Cosmos DB](partition-data.md).

