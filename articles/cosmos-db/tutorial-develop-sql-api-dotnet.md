---
title: 'Azure Cosmos DB : Développer avec l’API SQL dans .NET | Microsoft Docs'
description: Découvrez comment développer avec l’API SQL d’Azure Cosmos DB en utilisant .NET
services: cosmos-db
documentationcenter: ''
author: rafats
manager: jhubbard
editor: ''
tags: ''
ms.assetid: ''
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 05/10/2017
ms.author: rafats
ms.custom: mvc
ms.openlocfilehash: 7fca9f184097ed50ace665cde0c5ef8fb180feda
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="azure-cosmosdb-develop-with-the-sql-api-in-net"></a>Azure Cosmos DB : développer avec l’API SQL dans .NET

Azure Cosmos DB est le service de base de données multi-modèle de Microsoft distribué à l’échelle mondiale. Rapidement, vous avez la possibilité de créer et d’interroger des documents, des paires clé/valeur, et des bases de données orientées graphe, profitant tous de la distribution à l’échelle mondiale et des capacités de mise à l’échelle horizontale au cœur d’Azure Cosmos DB. 

Ce didacticiel montre comment créer un compte Azure Cosmos DB à l’aide du portail Azure, puis une base de données de documents et une collection avec une [clé de partition](sql-api-partition-data.md#partition-keys) à l’aide de [l’API .NET SQL](sql-api-introduction.md). En définissant une clé de partition au moment de créer une collection, vous préparez votre application et lui permettez d’être mise à l’échelle sans effort, à mesure que le volume de vos données augmente. 

Ce didacticiel décrit les tâches suivantes à l’aide de [l’API .NET SQL](sql-api-sdk-dotnet.md) :

> [!div class="checklist"]
> * Création d’un compte Azure Cosmos DB
> * Créer une base de données et une collection avec une clé de partition
> * Créer des documents JSON
> * Mettre à jour un document
> * Interroger des collections partitionnées
> * Exécuter des procédures stockées
> * Supprimer un document
> * Supprimer une base de données

## <a name="prerequisites"></a>Prérequis

Vérifiez que vous disposez des éléments suivants :

* Un compte Azure actif. Si vous n’en avez pas, vous pouvez vous inscrire pour bénéficier d’un [compte gratuit](https://azure.microsoft.com/free/). 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* Si vous n’avez pas encore installé Visual Studio 2017, vous pouvez télécharger et utiliser la version **gratuite** [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/). Veillez à activer **le développement Azure** lors de l’installation de Visual Studio.

## <a name="create-an-azure-cosmos-db-account"></a>Création d’un compte Azure Cosmos DB

Commençons par créer un compte Azure Cosmos DB dans le portail Azure.

> [!TIP]
> * Possédez-vous un compte Azure Cosmos DB ? Si tel est le cas, passez directement à l’étape [Configurer votre solution Visual Studio](#SetupVS).
> * Si vous utilisez l’émulateur Azure Cosmos DB, suivez les étapes de la section [Émulateur Azure Cosmos DB](local-emulator.md) pour le configurer, puis passez directement à l’étape [Configurer votre solution Visual Studio](#SetupVS). 
>
>

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="SetupVS"></a>Configurer votre solution Visual Studio
1. Ouvrez **Visual Studio** sur votre ordinateur.
2. Dans le menu **Fichier**, sélectionnez **Nouveau**, puis choisissez **Projet**.
3. Dans la boîte de dialogue **Nouveau projet**, sélectionnez **Modèles** / **Visual C#** / **Application console (.NET Framework)**, donnez un nom à votre projet, puis cliquez sur **OK**.
   ![Capture d’écran de la fenêtre Nouveau projet](./media/tutorial-develop-sql-api-dotnet/nosql-tutorial-new-project-2.png)

4. Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur votre nouvelle application console, qui se trouve sous votre solution Visual Studio, puis cliquez sur **Gérer les packages NuGet…**
    
    ![Capture d'écran du menu du clic droit pour le projet](./media/tutorial-develop-sql-api-dotnet/nosql-tutorial-manage-nuget-pacakges.png)
5. Dans l’onglet **NuGet**, cliquez sur **Parcourir**, puis tapez **documentdb** dans la zone de recherche.
<!---stopped here--->
6. Dans les résultats, recherchez **Microsoft.Azure.DocumentDB** et cliquez sur **Installer**.
   L’ID de package de la bibliothèque cliente Azure Cosmos DB est [Microsoft.Azure.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB).
   ![Capture d’écran du menu NuGet pour la recherche du Kit de développement logiciel (SDK) client Azure Cosmos DB](./media/tutorial-develop-sql-api-dotnet/nosql-tutorial-manage-nuget-pacakges-2.png)

    Si vous obtenez un message concernant la vérification des modifications apportées à la solution, cliquez sur **OK**. Si vous obtenez un message concernant l’acceptation de la licence, cliquez sur **J’accepte**.

## <a id="Connect"></a>Ajouter des références à votre projet
Les étapes restantes de ce didacticiel fournissent les extraits de code API SQL requis pour créer et mettre à jour des ressources Azure Cosmos DB dans votre projet.

Tout d’abord, ajoutez ces références à votre application.
<!---These aren't added by default when you install the pkg?--->

```csharp
using System.Net;
using Microsoft.Azure.Documents;
using Microsoft.Azure.Documents.Client;
using Newtonsoft.Json;
```

## <a id="add-references"></a>Connecter votre application

Ajoutez ces deux constantes et votre variable *client* à votre application.

```csharp
private const string EndpointUrl = "<your endpoint URL>";
private const string PrimaryKey = "<your primary key>";
private DocumentClient client;
```

Ensuite, revenez dans le [portail Azure](https://portal.azure.com) pour récupérer l’URL du point de terminaison et la clé primaire. L’URL du point de terminaison et la clé primaire sont nécessaires pour que votre application sache où se connecter et qu’Azure Cosmos DB approuve la connexion de votre application.

Dans le portail Azure, accédez à votre compte Azure Cosmos DB, cliquez sur **Clés**, puis sur **Clés en lecture-écriture**.

Copiez l’URI à partir du portail et collez-le dans `<your endpoint URL>`, dans le fichier program.cs. Copiez ensuite la CLÉ PRIMAIRE à partir du portail et collez-la dans `<your primary key>`. Veillez à supprimer `<` et `>` des valeurs.

![Capture d’écran du portail Azure utilisée par le didacticiel NoSQL pour créer une application de console C#. Indique un compte Azure Cosmos DB dont les CLÉS sont mises en surbrillance dans le panneau du compte Azure Cosmos DB et dont les valeurs d’URI et de CLÉ PRIMAIRE sont mises en surbrillance dans le volet Clés](./media/tutorial-develop-sql-api-dotnet/nosql-tutorial-keys.png)

## <a id="instantiate"></a>Instancier le DocumentClient

Maintenant, créez une instance de **DocumentClient**.

```csharp
DocumentClient client = new DocumentClient(new Uri(EndpointUrl), PrimaryKey);
```

## <a id="create-database"></a>Créer une base de données

Ensuite, créez une [base de données](sql-api-resources.md#databases) Azure Cosmos DB avec la méthode [CreateDatabaseAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdatabaseasync.aspx) ou la méthode [CreateDatabaseIfNotExistsAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdatabaseifnotexistsasync.aspx) de la classe **DocumentClient** provenant du [Kit SDK .NET SQL](sql-api-sdk-dotnet.md). Une base de données est le conteneur logique de stockage de documents JSON partitionné entre les collections.

```csharp
await client.CreateDatabaseAsync(new Database { Id = "db" });
```
## <a name="decide-on-a-partition-key"></a>Choisir une clé de partition 

Les collections sont des conteneurs servant au stockage des documents. Ce sont des ressources logiques. [Elles peuvent s’étendre sur une ou plusieurs partitions physiques](partition-data.md). Une [clé de partition](sql-api-partition-data.md) est une propriété (ou chemin d’accès) dans vos documents qui sert à distribuer vos données entre plusieurs serveurs ou partitions. Tous les documents présentant la même clé de partition sont stockés au sein de la même partition. 

Il est important de choisir une clé de partition avant de créer une collection. Les clés de partition sont une propriété (ou chemin d’accès) dans vos documents dont Azure Cosmos DB se sert pour distribuer vos données entre plusieurs serveurs ou partitions. Cosmos DB hache la valeur de clé de partition et utilise le résultat haché pour déterminer dans quelle partition stocker le document. Tous les documents ayant la même clé de partition sont stockés dans la même partition, et il est impossible de modifier les clés de partition après la création d’une collection. 

Dans le cadre de ce didacticiel, nous allons définir la clé de partition sur `/deviceId` afin que toutes les données d’un seul appareil soient stockées dans une partition unique. Vous souhaitez une clé de partition qui comporte un grand nombre de valeurs, chacune d’elles étant utilisées sur la même fréquence. Votre objectif ? Garantir que Cosmos DB puisse équilibrer la charge à mesure que le volume de vos données augmente et atteindre le débit maximal de la collection. 

Pour plus d’informations sur le partitionnement, voir [Comment partitionner et mettre à l’échelle dans Azure Cosmos DB ?](partition-data.md) 

## <a id="CreateColl"></a>Créer une collection 

Maintenant que nous connaissons notre clé de partition, `/deviceId`, créons une [collection](sql-api-resources.md#collections) à l’aide de la méthode [CreateDocumentCollectionAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdocumentcollectionasync.aspx) ou de la méthode [CreateDocumentCollectionIfNotExistsAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentcollectionifnotexistsasync.aspx) de la classe **DocumentClient**. Une collection est un conteneur de documents JSON. Elle est associée à une logique d’application JavaScript. 

> [!WARNING]
> La création d’une collection a des conséquences d’un point de vue tarifaire. En effet, vous réservez une part du débit pour que l’application puisse communiquer avec Azure Cosmos DB. Pour plus d'informations, visitez notre [page de tarification](https://azure.microsoft.com/pricing/details/cosmos-db/).
> 
> 

```csharp
// Collection for device telemetry. Here the JSON property deviceId is used  
// as the partition key to spread across partitions. Configured for 2500 RU/s  
// throughput and an indexing policy that supports sorting against any  
// number or string property. .
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "coll";
myCollection.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("db"),
    myCollection,
    new RequestOptions { OfferThroughput = 2500 });
```

Cette méthode passe un appel d’API REST à Azure Cosmos DB et le service approvisionne un certain nombre de partitions en fonction du débit demandé. Vous pouvez modifier le débit d’une collection à mesure que vos besoins en performances évoluent. Pour cela, vous utilisez le SDK ou le [portail Azure](set-throughput.md).

## <a id="CreateDoc"></a>Créer des documents JSON
Insérons maintenant des documents JSON dans Azure Cosmos DB. Vous pouvez créer un [document](sql-api-resources.md#documents) à l’aide de la méthode [CreateDocumentAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentasync.aspx) de la classe **DocumentClient**. Les documents sont du contenu JSON (arbitraire) défini par l'utilisateur. Cette classe utilisée à titre d’exemple contient la lecture d’un appareil et un appel à la méthode CreateDocumentAsync pour insérer une nouvelle lecture d’appareil dans une collection.

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

// Create a document. Here the partition key is extracted 
// as "XMS-0001" based on the collection definition
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
## <a name="read-data"></a>Lire les données

Lisons le document par sa clé de partition et son identifiant à l’aide de la méthode ReadDocumentAsync. Notez que les lectures incluent une valeur PartitionKey (correspondant à l’en-tête de demande `x-ms-documentdb-partitionkey` dans l’API REST).

```csharp
// Read document. Needs the partition key and the Id to be specified
Document result = await client.ReadDocumentAsync(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
  new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });

DeviceReading reading = (DeviceReading)(dynamic)result;
```

## <a name="update-data"></a>Mettre à jour des données

Maintenant, nous allons mettre à jour les données à l’aide de la méthode ReplaceDocumentAsync.

```csharp
// Update the document. Partition key is not required, again extracted from the document
reading.MetricValue = 104;
reading.ReadingTime = DateTime.UtcNow;

await client.ReplaceDocumentAsync(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
  reading);
```

## <a name="delete-data"></a>Suppression de données

Supprimons un document par sa clé de partition et son identifiant à l’aide de la méthode DeleteDocumentAsync.

```csharp
// Delete a document. The partition key is required.
await client.DeleteDocumentAsync(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
  new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });
```
## <a name="query-partitioned-collections"></a>Interroger des collections partitionnées

Lorsque vous interrogez des données dans des collections partitionnées, Azure Cosmos DB achemine automatiquement la requête vers les partitions correspondant aux valeurs de clé de partition spécifiées dans le filtre (le cas échéant). Par exemple, cette requête est acheminée uniquement vers la partition contenant la clé de partition « XMS-0001 ».

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

## <a name="parallel-query-execution"></a>Exécution de requêtes parallèles
Les kits de développement logiciel (SDK) SQL d’Azure Cosmos DB version 1.9.0 et versions ultérieures prennent en charge des options d’exécution de requêtes parallèles. Ils vous permettent d’effectuer des requêtes à faible latence sur les collections partitionnées, même lorsque ces requêtes concernent un grand nombre de partitions. Par exemple, la requête suivante est configurée pour s’exécuter en parallèle sur plusieurs partitions.

```csharp
// Cross-partition Order By queries
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("db", "coll"), 
    new FeedOptions { EnableCrossPartitionQuery = true, MaxDegreeOfParallelism = 10, MaxBufferedItemCount = 100})
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100)
    .OrderBy(m => m.MetricValue);
```
    
Vous pouvez gérer l’exécution de requêtes parallèles en réglant les paramètres suivants :

* En définissant `MaxDegreeOfParallelism`, vous pouvez contrôler le degré de parallélisme, c’est-à-dire le nombre maximal de connexions réseau simultanées aux partitions de la collection. Si vous définissez cette valeur sur -1, le degré de parallélisme est géré par le Kit de développement logiciel (SDK). Si la valeur `MaxDegreeOfParallelism` n’est pas spécifiée ou définie sur 0, qui est la valeur par défaut, il n’y aura qu’une seule connexion réseau aux partitions de la collection.
* En définissant `MaxBufferedItemCount`, vous pouvez compenser l’utilisation de la mémoire côté client et la latence de la requête. Si vous omettez ce paramètre ou que vous lui affectez la valeur -1, le nombre d’éléments mis en mémoire tampon pendant l’exécution de requêtes parallèles est géré par le Kit de développement logiciel (SDK).

Avec un même état de collection, une requête parallèle retourne les résultats dans l’ordre d’exécution en série. Lorsque vous effectuez une requête entre plusieurs partitions qui comporte le tri (ORDER BY et/ou TOP), le Kit de développement logiciel (SDK) SQL émet la requête en parallèle sur plusieurs partitions et fusionne les résultats partiellement triés côté client pour produire des résultats globalement classés.

## <a name="execute-stored-procedures"></a>Exécuter des procédures stockées
Enfin, vous pouvez également exécuter des transactions atomiques sur des documents avec le même ID d’appareil. C’est par exemple le cas lorsque vous réalisez la maintenance des agrégats ou que vous souhaitez obtenir le dernier état d’un appareil dans un document unique. Il vous suffit alors d’ajouter le code suivant à votre projet.

```csharp
await client.ExecuteStoredProcedureAsync<DeviceReading>(
    UriFactory.CreateStoredProcedureUri("db", "coll", "SetLatestStateAcrossReadings"),
    new RequestOptions { PartitionKey = new PartitionKey("XMS-001") }, 
    "XMS-001-FE24C");
```

Et le tour est joué ! Il s’agit des principaux composants d’une application Azure Cosmos DB qui utilise une clé de partition pour mettre efficacement à l’échelle la distribution des données sur plusieurs partitions.  

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous ne prévoyez pas de continuer à utiliser cette application, supprimez toutes les ressources créées par ce didacticiel dans le portail Azure. Pour cela, procédez comme suit :

1. Dans le menu de gauche du portail Azure, cliquez sur **Groupes de ressources**, puis sur le nom de la ressource que vous avez créée. 
2. Sur la page de votre groupe de ressources, cliquez sur **Supprimer**, tapez le nom de la ressource à supprimer dans la zone de texte, puis cliquez sur **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez : 

> [!div class="checklist"]
> * Créé un compte Azure Cosmos DB
> * Créé une base de données et une collection avec une clé de partition
> * Créé des documents JSON
> * Mis à jour un document
> * Interrogé des collections partitionnées
> * Exécuté une procédure stockée
> * Supprimé un document
> * Supprimé une base de données

Vous pouvez maintenant passer à l’étape suivante du didacticiel et importer des données supplémentaires dans votre compte Cosmos DB. 

> [!div class="nextstepaction"]
> [Importer des données dans Azure Cosmos DB](import-data.md)
