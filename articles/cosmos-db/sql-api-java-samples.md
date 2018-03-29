---
title: 'Azure Cosmos DB : exemples Java pour l’API SQL | Microsoft Docs'
description: Recherchez des exemples Java sur GitHub pour les tâches courantes utilisant l’API SQL Azure Cosmos DB, y compris les opérations CRUD.
keywords: Exemple NoSQL
services: cosmos-db
author: mimig1
manager: jhubbard
documentationcenter: java
ms.assetid: d824d517-903e-4d82-ab0a-09fc3b984c84
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 02/08/2018
ms.author: mimig
ms.openlocfilehash: 992451018baeea15bf63906c71ad72faccb0dbe4
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="azure-cosmos-db-java-examples-for-the-sql-api"></a>Azure Cosmos DB : exemples Java pour l’API SQL

> [!div class="op_single_selector"]
> * [Exemples .NET](sql-api-dotnet-samples.md)
> * [Exemples Java](sql-api-java-samples.md)
> * [Exemples Node.js](sql-api-nodejs-samples.md)
> * [Exemples Python](sql-api-python-samples.md)
> * [Galerie d’exemples de code Azure](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

Le référentiel GitHub [azure-documentdb-java](https://github.com/Azure/azure-documentdb-java) contient la dernière version des exemples d’applications qui exécutent des opérations CRUD, ainsi que d’autres opérations courantes sur les ressources Azure Cosmos DB. Cet article fournit :

* Des liens vers les tâches dans chacun des exemples de fichiers de projet Java. 
* Des liens vers le contenu de référence d’API connexe.

**Composants requis**

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
  
- Vous pouvez [activer les avantages de votre abonnement Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): ce dernier vous donne droit chaque mois à des crédits dont vous pouvez vous servir pour les services Azure payants.

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

Pour exécuter cet exemple d’application, vous avez besoin de ce qui suit :

* Kit de développement logiciel Java (JDK) 7
* Kit de développement logiciel (SDK) Microsoft Azure DocumentDB Java

Vous pouvez éventuellement utiliser Maven pour obtenir les derniers fichiers binaires du Kit de développement logiciel (SDK) de Microsoft Azure DocumentDB Java et les utiliser dans votre projet. Maven ajoute automatiquement toutes les dépendances nécessaires. Sinon, vous pouvez directement télécharger les dépendances répertoriées dans le fichier pom.xml et les ajouter à votre chemin d’accès de build.

```bash
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-documentdb</artifactId>
    <version>LATEST</version>
</dependency>
```

**Exécution des exemples d'applications**

Clonez l’exemple de référentiel :
```bash
$ git clone https://github.com/Azure/azure-documentdb-java.git

$ cd azure-documentdb-java
```

Vous pouvez exécuter les exemples en utilisant Eclipse ou depuis la ligne de commande en utilisant Maven.

Pour exécuter à partir d’Eclipse :
* Chargez le fichier pom.xml du projet parent principal dans Eclipse ; les exemples documentdb devraient être automatiquement chargés.
* Pour exécuter les exemples, vous avez besoin d’un point de terminaison Azure Cosmos DB valide. Les points de terminaison sont lus à partir de `src/test/java/com/microsoft/azure/documentdb/examples/AccountCredentials.java`.
* Vous pouvez transmettre les informations d’identification du point de terminaison en tant qu’arguments de la machine virtuelle dans la configuration de série de tests Eclipse JUnit ou bien vous pouvez les insérer dans AccountCredentials.java.
    ```bash
    -DACCOUNT_HOST="https://REPLACE_THIS.documents.azure.com:443/" -DACCOUNT_KEY="REPLACE_THIS"
    ```
* Vous pouvez maintenant exécuter les exemples en tant que tests JUnit dans Eclipse.

Pour exécuter depuis la ligne de commande :
* L’autre méthode d’exécution des exemples consiste à utiliser Maven :
* Exécutez Maven et transmettez vos informations d’identification du point de terminaison Azure Cosmos DB :
    ```bash
    mvn test -DACCOUNT_HOST="https://REPLACE_THIS_WITH_YOURS.documents.azure.com:443/" -DACCOUNT_KEY="REPLACE_THIS_WITH_YOURS"
    ```

   > [!NOTE]
   > Chaque exemple est autonome, se définit lui-même et se nettoie automatiquement. Les exemples transmettent plusieurs appels à [DocumentClient.createCollection](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._document_client.createcollection). À chaque appel, votre abonnement est facturé pour 1 heure d’utilisation selon le niveau de performances de la collection créée. 
   > 
   > 

## <a name="database-examples"></a>Exemples de base de données
Le fichier [DatabaseCrudSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DatabaseCrudSamples.java) montre comment effectuer les tâches suivantes :

| Tâche | Informations de référence sur l'API |
| --- | --- |
| [Création et lecture d’une base de données](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DatabaseCrudSamples.java#L64-L79) | [DocumentClient.createDatabase](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.createdatabase)<br>[DocumentClient.readDatabase](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.readdatabase)<br>[Resource.setId](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._resource.setid) |
| [Création et suppression d’une base de données](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DatabaseCrudSamples.java#L82-L93) | [DocumentClient.deleteDatabase](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.deletedatabase) |
| [Création et interrogation d’une base de données](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DatabaseCrudSamples.java#L96-L111) | [DocumentClient.queryDatabases](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.querydatabases) |

## <a name="collection-examples"></a>Exemples de collection
Le fichier [CollectionCrudSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java) montre comment effectuer les tâches suivantes :

| Tâche | Informations de référence sur l'API |
| --- | --- |
| [Création d’une collection à partition unique](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java#L74-L84) | [DocumentClient.createCollection](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._document_client.createcollection) |
| [Création d’une collection personnalisée à plusieurs partitions](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java#L103-L155) | [DocumentCollection](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._document_collection)<br>[PartitionKeyDefinition](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._partition_key_definition)<br>[RequestOptions](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._request_options) |
| [Supprimer une collection](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java#L97-L99) | [DocumentClient.deleteCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.deletecollection) |

## <a name="document-examples"></a>Exemples de document
Le fichier [DocumentCrudSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentCrudSamples.java) montre comment effectuer les tâches suivantes :

| Tâche | Informations de référence sur l'API |
| --- | --- |
| [Création, lecture et suppression d’un document](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentCrudSamples.java#L84-L122) | [DocumentClient.createDocument](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._document_client.createdocument)<br>[DocumentClient.readDocument](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._document_client.readdocument)<br>[DocumentClient.deleteDocument](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._document_client.deletedocument) |
| [Création d’un document avec une définition de document programmable](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentCrudSamples.java#L126-L147) | [Document](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._document)<br>[Resource.setId](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._resource.setid) |

## <a name="indexing-examples"></a>Exemples d'indexation
Le fichier [CollectionCrudSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java) montre comment effectuer les tâches suivantes :

| Tâche | Informations de référence sur l'API |
| --- | --- |
| [Création d’un index et définition d’une stratégie d’indexation](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java#L125-L141) | [Index](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._index)<br>[IndexingPolicy](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._indexing_policy) |

Pour plus d’informations sur l’indexation, consultez [Stratégies d’indexation d’Azure Cosmos DB](indexing-policies.md).

## <a name="query-examples"></a>Exemples de requête
Le fichier [DocumentQuerySamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentQuerySamples.java) montre comment effectuer les tâches suivantes :

| Tâche | Informations de référence sur l'API |
| --- | --- |
| [Exécution d’une requête de document simple sur des partitions croisées](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentQuerySamples.java#L108-L129) | [DocumentClient.queryDocuments](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._document_client.querydocuments)<br>[FeedOptions.setEnableCrossPartitionQuery](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._feed_options.setenablecrosspartitionquery) |
| [Requête Trier sur](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentQuerySamples.java#L132-L154) | [FeedResponse<T>.getQueryIterator](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._feed_response.getqueryiterator) |

Pour plus d’informations sur l’écriture de requêtes, voir [Requête SQL dans DAzure Cosmos DB](sql-api-sql-query.md).

## <a name="offer-examples"></a>Exemples d’offre
Le fichier [OfferCrudSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/OfferCrudSamples.java) montre comment effectuer les tâches suivantes :

| Tâche | Informations de référence sur l'API |
| --- | --- |
| [Création d’une collection et définition du débit](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/OfferCrudSamples.java#L76-L102) | [DocumentClient.createCollection](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._document_client.createcollection)<br>[RequestOptions.setOfferThroughput ](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._request_options.setofferthroughput) |
| [Lecture d’une collection pour rechercher l’offre associée](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/OfferCrudSamples.java#L108-L132) | [Offer.getContent](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._offer.getcontent)<br>[DocumentClient.replaceOffer](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._document_client.replaceoffer)<br>[DocumentClient.readCollection](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._document_client.readcollection)<br>[DocumentClient.queryOffers](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._document_client.queryoffers) |

## <a name="partition-key-examples"></a>Exemples de clé de partition
Le fichier [SinglePartitionCollectionDocumentCrudSample](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/SinglePartitionCollectionDocumentCrudSample.java) montre comment effectuer les tâches suivantes :

| Tâche | Informations de référence sur l'API |
| --- | --- |
| [Création d’une collection à partition unique](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/SinglePartitionCollectionDocumentCrudSample.java#L164-L207) | [DocumentClient.createCollection](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._document_client.createcollection) |
| [Modification de l’offre de débit pour une collection à partition unique](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/SinglePartitionCollectionDocumentCrudSample.java#L209-L223) | [DocumentClient.replaceOffer](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._document_client.replaceoffer) |

## <a name="stored-procedure-examples"></a>Exemples de procédure stockée
Le fichier [StoredProcedureSamples](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/StoredProcedureSamples.java) montre comment effectuer les tâches suivantes :

| Tâche | Informations de référence sur l'API |
| --- | --- |
| [Créer une procédure stockée](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/StoredProcedureSamples.java#L85-L118) | [StoredProcedure](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._stored_procedure)<br>[DocumentClient.createStoredProcedure](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._document_client.createstoredprocedure) |
| [Exécution d’une procédure stockée comportant des arguments](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/StoredProcedureSamples.java#L121-L144) | [DocumentClient.executeStoredProcedure](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._document_client.executestoredprocedure) |
| [Exécution d’une procédure stockée comportant un argument d’objet](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/StoredProcedureSamples.java#L147-L177) | [DocumentClient.executeStoredProcedure](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.documentdb._document_client.executestoredprocedure) |
