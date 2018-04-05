---
title: Indexation d’une source de données Azure Cosmos DB pour Recherche Azure | Microsoft Docs
description: Cet article explique comment créer un indexeur Recherche Azure avec une source de données Azure Cosmos DB.
services: search
documentationcenter: ''
author: chaosrealm
manager: pablocas
editor: ''
ms.assetid: ''
ms.service: search
ms.devlang: rest-api
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: search
ms.date: 03/23/2018
ms.author: eugenesh
robot: noindex
ms.openlocfilehash: 165402f5147224cd355f0ae14642069a3de58f19
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="connecting-cosmos-db-with-azure-search-using-indexers"></a>Connexion de Cosmos DB à Recherche Azure à l’aide d’indexeurs

Dans cet article, découvrez comment :

> [!div class="checklist"]
> * Configurer un [indexeur Recherche Azure](search-indexer-overview.md) qui utilise une collection Azure Cosmos DB en tant que source de données.
> * créer un index de recherche avec des types de données compatibles avec JSON ;
> * configurer un indexeur à des fins d’indexation périodique et à la demande ;
> * actualiser l’index de manière incrémentielle en fonction des modifications apportées aux données sous-jacentes ;

> [!NOTE]
> Azure Cosmos DB est la nouvelle génération de DocumentDB. Bien que le nom du produit ait évolué, la syntaxe `documentdb` utilisée dans les indexeurs Recherche Azure existe toujours pour la compatibilité descendante sur les pages du portail et dans les API Recherche Azure. Lorsque vous configurez des indexeurs, veillez à spécifier la syntaxe `documentdb`, suivant les instructions de cet article.

Dans la vidéo suivante, Andrew Liu, chef de programme Azure Cosmos DB, explique comment ajouter un index Recherche Azure à un conteneur Azure Cosmos DB.

>[!VIDEO https://www.youtube.com/embed/OyoYu1Wzk4w]

<a name="supportedAPIs"></a>
## <a name="supported-api-types"></a>Types d’API pris en charge

Bien qu’Azure Cosmos DB prenne en charge un large éventail de modèles de données et d’API, la prise en charge en production de l’indexeur Recherche Azure s’étend seulement à l’API SQL. L’API MongoDB est actuellement prise en charge en préversion publique.  

La prise en charge d’API supplémentaires arrivera prochainement. Pour nous aider à identifier les API à prendre en charge en priorité, nous vous invitons à voter sur le site web UserVoice :

* [Prise en charge de la source de données API Table](https://feedback.azure.com/forums/263029-azure-search/suggestions/32759746-azure-search-should-be-able-to-index-cosmos-db-tab)
* [Prise en charge de la source de données API Graph](https://feedback.azure.com/forums/263029-azure-search/suggestions/13285011-add-graph-databases-to-your-data-sources-eg-neo4)
* [Prise en charge de la source de données API Apache Cassandra](https://feedback.azure.com/forums/263029-azure-search/suggestions/32857525-indexer-crawler-for-apache-cassandra-api-in-azu)

## <a name="prerequisites"></a>Prérequis


En plus d’un compte Cosmos DB, vous devez disposer d’un [service Recherche Azure](search-create-service-portal.md). 

<a name="Concepts"></a>
## <a name="azure-search-indexer-concepts"></a>Concepts d’indexeur Azure Search

Une **source de données** spécifie les données à indexer, les informations d’identification et les stratégies pour identifier les modifications des données (par exemple, les documents modifiés ou supprimés dans votre collection). La source de données est définie en tant que ressource indépendante de manière à pouvoir être utilisée par plusieurs indexeurs.

Un **indexeur** décrit le flux de données de votre source de données vers un index de recherche cible. Un indexeur peut servir à :

* effectuer une copie unique des données pour remplir un index ;
* synchroniser un index avec les modifications apportées à la source de données selon une planification.
* Appeler des mises à jour d'un index à la demande en fonction des besoins.

Pour configurer un indexeur Azure Cosmos DB, vous devez créer un index, une source de données et enfin l’indexeur. Vous pouvez créer ces objets à l’aide du [portail](search-import-data-portal.md), du [Kit de développement logiciel (SDK) .NET](/dotnet/api/microsoft.azure.search) ou de l’[API REST](/rest/api/searchservice/). 

Cet article explique comment le faire à l’aide de l’API REST. Si vous optez pour le portail, [l’Assistant Importation de données](search-import-data-portal.md) vous guidera dans la création de toutes ces ressources, index compris.

> [!TIP]
> Vous pouvez lancer l’Assistant **Importation de données** sur le tableau de bord Azure Cosmos DB afin de simplifier l’indexation de cette source de données. Dans la navigation de gauche, accédez à **Collections** > **Ajouter la Recherche Azure** pour commencer.

> [!NOTE] 
> Pour l’instant, vous ne pouvez pas créer ou modifier de sources de données **MongoDB** à l’aide du portail Azure ou du Kit de développement logiciel (SDK) .NET. Cependant, vous **pouvez** surveiller l’historique d’exécution des indexeurs MongoDB dans le portail.  

<a name="CreateDataSource"></a>
## <a name="step-1-create-a-data-source"></a>Étape 1 : Création d’une source de données
Pour créer une source de données, effectuez un POST :

    POST https://[service name].search.windows.net/datasources?api-version=2016-09-01
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mydocdbdatasource",
        "type": "documentdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myCosmosDbEndpoint.documents.azure.com;AccountKey=myCosmosDbAuthKey;Database=myCosmosDbDatabaseId"
        },
        "container": { "name": "myCollection", "query": null },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "_ts"
        }
    }

Le corps de la requête contient la définition de la source de données, qui doit inclure les champs suivants :

* **name** : choisissez un nom qui représentera votre base de données.
* **type** : doit être `documentdb`.
* **credentials**:
  
  * **connectionString**: obligatoire. Spécifiez les informations de connexion à votre base de données Azure Cosmos DB dans le format suivant : `AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>` Pour les collections MongoDB, ajoutez **ApiKind=MongoDB** à la chaîne de connexion : `AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>;ApiKind=MongoDB` 
* **container**:
  
  * **name**: obligatoire. Spécifiez l’ID de la collection de bases de données à indexer.
  * **query**: facultatif. Vous pouvez spécifier une requête pour obtenir un schéma plat à partir d'un document JSON arbitraire de manière à ce qu'Azure Search puisse procéder à l'indexation. Pour les collections MongoDB, les requêtes ne sont pas prises en charge. 
* **dataChangeDetectionPolicy** : recommandé. Consultez la section [Indexation des documents modifiés](#DataChangeDetectionPolicy).
* **dataDeletionDetectionPolicy**: facultatif. Consultez la section [Indexation des documents supprimés](#DataDeletionDetectionPolicy).

### <a name="using-queries-to-shape-indexed-data"></a>Utilisation de requêtes pour formater les données indexées
Vous pouvez spécifier une requête SQL pour aplatir les propriétés ou les tableaux imbriqués, projeter des propriétés JSON et filtrer les données à indexer. 

> [!WARNING]
> Les requêtes personnalisées ne sont pas pris en charge pour les collections **MongoDB** : le paramètre `container.query` doit être défini sur Null ou être omis. Si vous avez besoin d’utiliser une requête personnalisée, indiquez-le nous sur [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

Exemple de document :

    {
        "userId": 10001,
        "contact": {
            "firstName": "andy",
            "lastName": "hoh"
        },
        "company": "microsoft",
        "tags": ["azure", "documentdb", "search"]
    }

Requête de filtre :

    SELECT * FROM c WHERE c.company = "microsoft" and c._ts >= @HighWaterMark ORDER BY c._ts

Requête d’aplatissage :

    SELECT c.id, c.userId, c.contact.firstName, c.contact.lastName, c.company, c._ts FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts
    
    
Requête de projection :

    SELECT VALUE { "id":c.id, "Name":c.contact.firstName, "Company":c.company, "_ts":c._ts } FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts


Requête d’aplatissage de tableau :

    SELECT c.id, c.userId, tag, c._ts FROM c JOIN tag IN c.tags WHERE c._ts >= @HighWaterMark ORDER BY c._ts

<a name="CreateIndex"></a>
## <a name="step-2-create-an-index"></a>Étape 2 : Création d’un index
Créez un index Azure Search cible si vous n'en possédez pas déjà un. Vous pouvez créer un index avec [l’interface utilisateur du portail Azure](search-create-index-portal.md), [l’API Création d’index](/rest/api/searchservice/create-index) ou la [classe Index](/dotnet/api/microsoft.azure.search.models.index).

L'exemple suivant crée un index avec un champ ID et un champ Description :

    POST https://[service name].search.windows.net/indexes?api-version=2016-09-01
    Content-Type: application/json
    api-key: [Search service admin key]

    {
       "name": "mysearchindex",
       "fields": [{
         "name": "id",
         "type": "Edm.String",
         "key": true,
         "searchable": false
       }, {
         "name": "description",
         "type": "Edm.String",
         "filterable": false,
         "sortable": false,
         "facetable": false,
         "suggestions": true
       }]
     }

Assurez-vous que le schéma de votre index cible est compatible avec le schéma des documents JSON source ou la sortie de votre projection de requête personnalisée.

> [!NOTE]
> Pour les collections partitionnées, la clé de document par défaut est la propriété `_rid` d’Azure Cosmos DB, que Recherche Azure renomme automatiquement `rid`, car les noms de champ ne peuvent pas commencer par un trait de soulignement. De même, les valeurs `_rid` d’Azure Cosmos DB contiennent des caractères non valides dans les clés de Recherche Azure. Par conséquent, les valeurs `_rid` sont codées en Base64.
> 
> Pour les collections MongoDB, Recherche Azure renomme automatiquement la propriété `_id` `doc_id`.  

### <a name="mapping-between-json-data-types-and-azure-search-data-types"></a>Mappage entre les types de données JSON et les types de données Azure Search
| Type de données JSON | Types de champs d’index cible compatibles |
| --- | --- |
| Bool |Edm.Boolean, Edm.String |
| Nombres qui ressemblent à des nombres entiers |Edm.Int32, Edm.Int64, Edm.String |
| Nombres qui ressemblent à des nombres avec points flottants |Edm.Double, Edm.String |
| Chaîne |Edm.String |
| Tableaux de types primitifs, par exemple ["a", "b", "c"] |Collection(Edm.String) |
| Chaînes qui ressemblent à des dates |Edm.DateTimeOffset, Edm.String |
| Objets GeoJSON, par exemple { "type": "Point", "coordinates": [long, lat] } |Edm.GeographyPoint |
| Autres objets JSON |N/A |

<a name="CreateIndexer"></a>

## <a name="step-3-create-an-indexer"></a>Étape 3 : Création d’un indexeur

Une fois l'index et la source de données créés, vous êtes prêt à créer l’indexeur :

    POST https://[service name].search.windows.net/indexers?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "mydocdbindexer",
      "dataSourceName" : "mydocdbdatasource",
      "targetIndexName" : "mysearchindex",
      "schedule" : { "interval" : "PT2H" }
    }

Cet indexeur s’exécute toutes les deux heures (intervalle de planification défini sur « PT2H »). Pour exécuter un indexeur toutes les 30 minutes, définissez l’intervalle sur « PT30M ». Le plus court intervalle pris en charge est de 5 minutes. La planification est facultative : en cas d’omission, un indexeur ne s’exécute qu’une seule fois lorsqu’il est créé. Toutefois, vous pouvez à tout moment exécuter un indexeur à la demande.   

Pour plus d’informations sur l’API Créer un indexeur, consultez [Créer un indexeur](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

<a id="RunIndexer"></a>
### <a name="running-indexer-on-demand"></a>Exécution de l’indexeur à la demande
En plus de l'exécution périodique planifiée, un indexeur peut également être appelé à la demande :

    POST https://[service name].search.windows.net/indexers/[indexer name]/run?api-version=2016-09-01
    api-key: [Search service admin key]

> [!NOTE]
> Lors de l’API s’exécute avec succès, l’appel de l’indexeur a été planifié, mais le traitement réel se produit de façon asynchrone. 

Vous pouvez surveiller l’état de l’indexeur dans le portail ou à l’aide de l’API Get Indexer Status, que nous décrivons par la suite. 

<a name="GetIndexerStatus"></a>
### <a name="getting-indexer-status"></a>Obtention de l’état de l’indexeur
Vous pouvez récupérer l'historique d'état et d'exécution d'un indexeur :

    GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2016-09-01
    api-key: [Search service admin key]

La réponse contient l'état d'intégrité global de l'indexeur, le dernier appel de l'indexeur (ou celui en cours), ainsi que l'historique des appels récents de l'indexeur.

    {
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2014-11-26T03:37:18.853Z",
            "endTime":"2014-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
         },
        "executionHistory":[ {
            "status":"success",
             "errorMessage":null,
            "startTime":"2014-11-26T03:37:18.853Z",
            "endTime":"2014-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
        }]
    }

L'historique d'exécution contient les 50 exécutions les plus récentes, classées par ordre chronologique inverse (la dernière exécution est répertoriée en premier dans la réponse).

<a name="DataChangeDetectionPolicy"></a>
## <a name="indexing-changed-documents"></a>Indexation des documents modifiés
L'objectif d'une stratégie de détection des changements de données est d'identifier efficacement les données modifiées. La seule stratégie actuellement prise en charge est la stratégie `High Water Mark` qui utilise la propriété `_ts` (timestamp) fournie par Azure Cosmos DB, définie ainsi :

    {
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "_ts"
    }

Cette stratégie est vivement recommandée pour garantir de bonnes performances pour l’indexeur. 

Si vous utilisez une requête personnalisée, assurez-vous que la propriété `_ts` est projetée par la requête.

<a name="IncrementalProgress"></a>
### <a name="incremental-progress-and-custom-queries"></a>Progression incrémentielle et requêtes personnalisées
Dans le cas où l’exécution de l’indexeur est interrompue par des échecs passagers ou un dépassement du délai d’exécution, une progression incrémentielle pendant une indexation veille à ce que l’indexeur puisse reprendre là il en était lors de sa dernière exécution, afin de ne pas avoir à tout réindexer depuis le début. Ceci est particulièrement important lors de l’indexation de grandes collections. 

Pour activer la progression incrémentielle lors de l’utilisation d’une requête personnalisée, assurez-vous que votre requête classe les résultats par la colonne `_ts`. Ceci permet de créer des points de contrôle périodiques dont Azure Search se sert pour proposer la progression incrémentielle en cas d’erreurs.   

Dans certains cas, il se peut qu’Azure Search ne déduise pas que la requête est ordonnée par `_ts`, même si elle contient une clause `ORDER BY [collection alias]._ts`. Vous pouvez indiquer à Azure Search que les résultats sont triés à l’aide de la propriété de configuration `assumeOrderByHighWaterMarkColumn`. Pour ce faire, créez ou mettez à jour l’indexeur comme suit : 

    {
     ... other indexer definition properties
     "parameters" : {
            "configuration" : { "assumeOrderByHighWaterMarkColumn" : true } }
    } 

<a name="DataDeletionDetectionPolicy"></a>
## <a name="indexing-deleted-documents"></a>Indexation des documents supprimés
Lorsque des lignes sont supprimées de la collection, vous devez normalement supprimer ces lignes de l'index de recherche. L'objectif d'une stratégie de détection des suppressions de données est d'identifier efficacement les données supprimées. La seule stratégie actuellement prise en charge est la stratégie `Soft Delete` (où la suppression est signalée par un indicateur quelconque), spécifiée comme suit :

    {
        "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
        "softDeleteColumnName" : "the property that specifies whether a document was deleted",
        "softDeleteMarkerValue" : "the value that identifies a document as deleted"
    }

Si vous utilisez une requête personnalisée, assurez-vous que la propriété référencée par `softDeleteColumnName` est projetée par la requête.

L'exemple suivant crée une source de données avec des conseils pour une stratégie de suppression en douceur :

    POST https://[Search service name].search.windows.net/datasources?api-version=2016-09-01
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mydocdbdatasource",
        "type": "documentdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myDocDbEndpoint.documents.azure.com;AccountKey=myDocDbAuthKey;Database=myDocDbDatabaseId"
        },
        "container": { "name": "myDocDbCollectionId" },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "_ts"
        },
        "dataDeletionDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName": "isDeleted",
            "softDeleteMarkerValue": "true"
        }
    }

## <a name="NextSteps"></a>Étapes suivantes
Félicitations ! Vous avez appris à intégrer Azure Cosmos DB avec Recherche Azure à l’aide d’un indexeur.

* Pour en savoir plus sur Azure Cosmos DB, consultez la [page du service Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).
* Pour en savoir plus sur la Recherche Azure, consultez la [page du service Recherche](https://azure.microsoft.com/services/search/).
