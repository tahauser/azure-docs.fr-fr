---
title: "Partitionnement et mise à l’échelle dans Azure Cosmos DB | Microsoft Docs"
description: "Découvrez comment le partitionnement fonctionne dans Azure Cosmos DB, comment configurer le partitionnement et les clés de partition, et comment choisir la clé de partition appropriée pour votre application."
services: cosmos-db
author: rafats
manager: jhubbard
documentationcenter: 
ms.assetid: 702c39b4-1798-48dd-9993-4493a2f6df9e
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/24/2017
ms.author: rafats
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bfed50eef02c237ce0ea4480e2e208f2e61ccbef
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2017
---
# <a name="partitioning-in-azure-cosmos-db-using-the-sql-api"></a>Partitionnement dans Azure Cosmos DB à l’aide de l’API SQL

[!INCLUDE [cosmos-db-sql-api](../../includes/cosmos-db-sql-api.md)]

[Microsoft Azure Cosmos DB](../cosmos-db/introduction.md) est un service de base de données multi-modèle, distribué globalement conçu pour vous aider à obtenir des performances rapides et prévisibles, et à monter en charge en toute transparence parallèlement à l’évolution de votre application. 

Cet article fournit une vue d’ensemble de l’utilisation du partitionnement des conteneurs Cosmos DB avec l’API SQL. Consultez [Partitionnement et mise à l’échelle horizontale](../cosmos-db/partition-data.md) afin de découvrir une vue d’ensemble des concepts et bonnes pratiques pour le partitionnement avec l’API Azure Cosmos DB. 

Pour commencer avec le code, téléchargez le projet à partir de [GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/a2d61ddb53f8ab2a23d3ce323c77afcf5a608f52/samples/documentdb-benchmark). 

Après avoir lu cet article, vous serez en mesure de répondre aux questions suivantes :   

* Comment le partitionnement dans Azure Cosmos DB fonctionne-t-il ?
* Comment configurer le partitionnement dans Azure Cosmos DB ?
* Que sont les clés de partition et comment choisir la bonne clé de partition pour mon application ?

Pour commencer avec le code, téléchargez le projet à partir de l’[exemple de pilote de test des performances Azure Cosmos DB](https://github.com/Azure/azure-documentdb-dotnet/tree/a2d61ddb53f8ab2a23d3ce323c77afcf5a608f52/samples/documentdb-benchmark). 

<!-- placeholder until we have a permanent solution-->
<a name="partition-keys"></a>
<a name="single-partition-and-partitioned-collections"></a>
<a name="migrating-from-single-partition"></a>

## <a name="partition-keys"></a>Clés de partition

Dans l’API SQL, vous spécifiez la définition de la clé de partition sous la forme d’un chemin d’accès JSON. Le tableau suivant présente des exemples de définitions de clé de partition ainsi que les valeurs correspondantes. La clé de partition est spécifiée en tant que chemin d’accès. Par exemple, `/department` représente le service de la propriété. 

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p><strong>Clé de partition</strong></p></td>
            <td valign="top"><p><strong>Description</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>/department</p></td>
            <td valign="top"><p>Correspond à la valeur de doc.department, où doc est l’élément.</p></td>
        </tr>
        <tr>
            <td valign="top"><p>/properties/name</p></td>
            <td valign="top"><p>Correspond à la valeur de doc.properties.name, où doc est l’élément (propriété imbriquée).</p></td>
        </tr>
        <tr>
            <td valign="top"><p>/id</p></td>
            <td valign="top"><p>Correspond à la valeur de doc.id (l’ID et la clé de partition sont la même propriété).</p></td>
        </tr>
        <tr>
            <td valign="top"><p>/"nom de service"</p></td>
            <td valign="top"><p>Correspond à la valeur de doc["nom de service"], où doc est l’élément.</p></td>
        </tr>
    </tbody>
</table>

> [!NOTE]
> La syntaxe de la clé de partition est semblable à la spécification de chemin d’accès pour l’indexation des chemins d’accès de stratégie, à la différence notable que le chemin d’accès correspond à la propriété plutôt qu’à la valeur, de sorte qu’il n’y a pas de caractère générique à la fin. Par exemple, vous devez spécifier /department/? pour indexer les valeurs sous le service, mais spécifier /department comme définition de clé de partition. La clé de partition est indexée de façon implicite et ne peut pas être exclue de l’indexation à l’aide de remplacements de la stratégie d’indexation.
> 
> 

Observons comment le choix de la clé de partition a une incidence sur les performances de votre application.

## <a name="working-with-the-azure-cosmos-db-sdks"></a>Utilisation des kits SDK Azure Cosmos DB
Avec l’[API REST version 2015-12-16](/rest/api/documentdb/), Azure Cosmos DB a ajouté la prise en charge du partitionnement automatique. Afin de créer des conteneurs partitionnés, vous devez télécharger le Kit de développement logiciel (SDK) version 1.6.0 ou une version plus récente sur l’une des plateformes SDK prises en charge (.NET, Node.js, Java, Python, MongoDB). 

### <a name="creating-containers"></a>Création de conteneurs
L’exemple suivant montre un extrait de code .NET permettant de créer un conteneur pour stocker les données de télémétrie d’appareil avec un débit de 20 000 unités de demande par seconde. Le Kit de développement logiciel (SDK) définit la valeur OfferThroughput (qui définit à son tour l’en-tête de demande `x-ms-offer-throughput` dans l’API REST). Ici, nous définissons `/deviceId` comme clé de partition. Le choix de la clé de partition est enregistré avec le reste des métadonnées du conteneur, telles que le nom et la stratégie d’indexation.

Pour cet exemple, nous avons choisi `deviceId` , car nous savons que (a) dans la mesure où il existe un grand nombre d’appareils, les écritures peuvent être réparties uniformément entre les partitions, ce qui permet de mettre à l’échelle la base de données pour recevoir de très gros volumes de données et (b) plusieurs requêtes, comme l’extraction de la dernière lecture d’un appareil, sont limitées à un identificateur d’appareil unique et peuvent être récupérées à partir d’une seule partition.

```csharp
DocumentClient client = new DocumentClient(new Uri(endpoint), authKey);
await client.CreateDatabaseAsync(new Database { Id = "db" });

// Container for device telemetry. Here the property deviceId will be used as the partition key to 
// spread across partitions. Configured for 10K RU/s throughput and an indexing policy that supports 
// sorting against any number or string property.
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "coll";
myCollection.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("db"),
    myCollection,
    new RequestOptions { OfferThroughput = 20000 });
```

Cette méthode passe un appel de l’API REST à Cosmos DB, et le service approvisionne plusieurs partitions en fonction du débit demandé. Vous pouvez modifier le débit d’un conteneur à mesure que vos besoins en matière de performances évoluent. 

### <a name="reading-and-writing-items"></a>Lecture et écriture d’éléments
À présent, nous allons insérer des données dans Cosmos DB. Voici un exemple de classe qui contient la lecture d’un appareil et un appel à CreateDocumentAsync pour insérer une nouvelle lecture d’appareil dans un conteneur. Voici un exemple recourant à l’API SQL :

```csharp
public class DeviceReading
{
    [JsonProperty("id")]
    public string Id;

    [JsonProperty("deviceId")]
    public string DeviceId;

    [JsonConverter(typeof(IsoDateTimeConverter))]
    [JsonProperty("readingTime")]
    public DateTime ReadingTime;

    [JsonProperty("metricType")]
    public string MetricType;

    [JsonProperty("unit")]
    public string Unit;

    [JsonProperty("metricValue")]
    public double MetricValue;
  }

// Create a document. Here the partition key is extracted as "XMS-0001" based on the collection definition
await client.CreateDocumentAsync(
    UriFactory.CreateDocumentCollectionUri("db", "coll"),
    new DeviceReading
    {
        Id = "XMS-001-FE24C",
        DeviceId = "XMS-0001",
        MetricType = "Temperature",
        MetricValue = 105.00,
        Unit = "Fahrenheit",
        ReadingTime = DateTime.UtcNow
    });
```

Nous allons lire l’élément avec sa clé de partition et son ID, le mettre à jour et, dans un dernier temps, nous allons le supprimer par clé de partition et ID. Notez que les lectures incluent une valeur PartitionKey (correspondant à l’en-tête de demande `x-ms-documentdb-partitionkey` dans l’API REST).

```csharp
// Read document. Needs the partition key and the ID to be specified
Document result = await client.ReadDocumentAsync(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
  new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });

DeviceReading reading = (DeviceReading)(dynamic)result;

// Update the document. Partition key is not required, again extracted from the document
reading.MetricValue = 104;
reading.ReadingTime = DateTime.UtcNow;

await client.ReplaceDocumentAsync(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
  reading);

// Delete document. Needs partition key
await client.DeleteDocumentAsync(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
  new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });
```

### <a name="querying-partitioned-containers"></a>Interrogation de conteneurs partitionnés
Lorsque vous interrogez des données dans des conteneurs partitionnés, Cosmos DB achemine automatiquement la requête vers les partitions correspondant aux valeurs de clé de partition spécifiées dans le filtre (le cas échéant). Par exemple, cette requête est acheminée uniquement vers la partition contenant la clé de partition « XMS-0001 ».

```csharp
// Query using partition key
IQueryable<DeviceReading> query = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("db", "coll"))
    .Where(m => m.MetricType == "Temperature" && m.DeviceId == "XMS-0001");
```
    
Pour la requête suivante, la clé de partition (DeviceId) n’a pas de filtre. La requête est donc distribuée à toutes les partitions où elle est exécutée sur l’index de la partition. Notez que vous devez spécifier la valeur EnableCrossPartitionQuery (`x-ms-documentdb-query-enablecrosspartition` dans l’API REST) pour que le Kit de développement logiciel (SDK) exécute une requête sur plusieurs partitions.

```csharp
// Query across partition keys
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("db", "coll"), 
    new FeedOptions { EnableCrossPartitionQuery = true })
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100);
```

Cosmos DB prend en charge les [fonctions d’agrégation](sql-api-sql-query.md#Aggregates) `COUNT`, `MIN`, `MAX`, `SUM` et `AVG` sur des conteneurs partitionnés à l’aide de SQL à partir des Kits de développement logiciel (SDK) 1.12.0 et versions ultérieures. Les requêtes doivent comporter un opérateur d’agrégation unique et doivent inclure une valeur unique dans la projection.

### <a name="parallel-query-execution"></a>Exécution de requêtes parallèles
Les kits de développement logiciel (SDK) de Cosmos DB version 1.9.0 et versions ultérieures prennent en charge des options d’exécution de requêtes parallèles, ce qui vous permet d’effectuer des requêtes à faible latence sur les collections partitionnées, même lorsque ces requêtes concernent un grand nombre de partitions. Par exemple, la requête suivante est configurée pour s’exécuter en parallèle sur plusieurs partitions.

```csharp
// Cross-partition Order By Queries
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("db", "coll"), 
    new FeedOptions { EnableCrossPartitionQuery = true, MaxDegreeOfParallelism = 10, MaxBufferedItemCount = 100})
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100)
    .OrderBy(m => m.MetricValue);
```
    
Vous pouvez gérer l’exécution de requêtes parallèles en réglant les paramètres suivants :

* En définissant `MaxDegreeOfParallelism`, vous pouvez contrôler le degré de parallélisme, c’est-à-dire le nombre maximal de connexions réseau simultanées aux partitions du conteneur. Si vous définissez cette valeur sur -1, le degré de parallélisme est géré par le Kit de développement logiciel (SDK). Si la valeur `MaxDegreeOfParallelism` n’est pas spécifiée ou définie sur 0, qui est la valeur par défaut, il n’y a qu’une seule connexion réseau aux partitions du conteneur.
* En définissant `MaxBufferedItemCount`, vous pouvez compenser l’utilisation de la mémoire côté client et la latence de la requête. Si vous omettez ce paramètre ou que vous lui affectez la valeur -1, le nombre d’éléments mis en mémoire tampon pendant l’exécution de requêtes parallèles est géré par le Kit de développement logiciel (SDK).

Avec un même état de collection, une requête parallèle retourne les résultats dans l’ordre d’exécution en série. Lorsque vous effectuez une requête sur plusieurs partitions qui inclut le tri (ORDER BY et/ou TOP), le kit SDK d’Azure Cosmos DB émet la requête en parallèle sur plusieurs partitions et fusionne les résultats partiellement triés côté client pour produire des résultats classés globalement.

### <a name="executing-stored-procedures"></a>Exécution de procédures stockées
Vous pouvez également exécuter des transactions atomiques sur des documents avec le même ID d’appareil, par exemple, si vous conservez des agrégats ou le dernier état d’un appareil dans un élément unique. 

```csharp
await client.ExecuteStoredProcedureAsync<DeviceReading>(
    UriFactory.CreateStoredProcedureUri("db", "coll", "SetLatestStateAcrossReadings"),
    new RequestOptions { PartitionKey = new PartitionKey("XMS-001") }, 
    "XMS-001-FE24C");
```
   
Dans la section suivante, nous examinons la manière de passer des conteneurs à partition unique à des conteneurs partitionnés.

## <a name="next-steps"></a>Étapes suivantes
Dans cet article, nous avons fourni une vue d’ensemble de l’utilisation du partitionnement des conteneurs Azure Cosmos DB avec l’API SQL. Consultez également [Partitionnement et mise à l’échelle horizontale](../cosmos-db/partition-data.md) pour découvrir une vue d’ensemble des concepts et bonnes pratiques pour le partitionnement avec l’API Azure Cosmos DB. 

* Effectuez un test des performances et de la mise à l’échelle avec Azure Cosmos DB. Consultez la page [Test des performances et de la mise à l’échelle avec Azure Cosmos DB](performance-testing.md) pour obtenir un exemple.
* Commencez à coder avec les [Kits de développement logiciel (SDK)](sql-api-sdk-dotnet.md) ou [l’API REST](/rest/api/documentdb/)
* Apprenez-en davantage sur le [débit approvisionné dans Azure Cosmos DB](request-units.md)

