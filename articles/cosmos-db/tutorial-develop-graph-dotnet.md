---
title: "Azure Cosmos DB : Développer avec l’API Graph dans .NET | Microsoft Docs"
description: "Découvrez comment développer avec l’API SQL d’Azure Cosmos DB en utilisant .NET"
services: cosmos-db
documentationcenter: 
author: luisbosquez
manager: jhubbard
editor: 
ms.assetid: cc8df0be-672b-493e-95a4-26dd52632261
ms.service: cosmos-db
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 01/02/2018
ms.author: lbosq
ms.custom: mvc
ms.openlocfilehash: ddbfe11e4415e1c240914142f4daf54b3032f5d8
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="azure-cosmos-db-develop-with-the-graph-api-in-net"></a>Azure Cosmos DB : Développer avec l’API Graph dans .NET
Azure Cosmos DB est le service de base de données multi-modèle de Microsoft distribué à l’échelle mondiale. Rapidement, vous avez la possibilité de créer et d’interroger des documents, des paires clé/valeur, et des bases de données orientées graphe, profitant tous de la distribution à l’échelle mondiale et des capacités de mise à l’échelle horizontale au cœur d’Azure Cosmos DB. 

Ce didacticiel montre comment créer un compte Azure Cosmos DB à l’aide du portail Azure, puis une base de données de graphiques et un conteneur. L’application crée ensuite un réseau social simple composé de quatre personnes à l’aide de [l’API Graph](graph-sdk-dotnet.md), puis parcourt et interroge le graphique à l’aide de Gremlin.

Ce didacticiel décrit les tâches suivantes :

> [!div class="checklist"]
> * Créer un compte Azure Cosmos DB 
> * Créer une base de données des graphiques et un conteneur
> * Sérialiser des vertex et des bords sur des objets .NET
> * Ajouter des vertex et des bords
> * Interroger le graphique à l’aide de Gremlin

## <a name="graphs-in-azure-cosmos-db"></a>Graphiques dans Azure Cosmos DB
Vous pouvez utiliser Azure Cosmos DB pour créer, mettre à jour et interroger les graphiques à l’aide de la bibliothèque [Microsoft.Azure.Graphs](graph-sdk-dotnet.md). La bibliothèque Microsoft.Azure.Graph fournit une méthode d’extension unique `CreateGremlinQuery<T>` en haut de la classe `DocumentClient` pour exécuter des requêtes Gremlin.

Gremlin est un langage de programmation fonctionnel qui prend en charge les opérations d’écriture (DML) et les opérations de requête et de traversée. Nous abordons quelques exemples dans cet article pour vous permettre de prendre en main Gremlin. Consultez [Requêtes Gremlin](gremlin-support.md) pour une description détaillée des fonctionnalités de Gremlin disponibles dans Azure Cosmos DB. 

## <a name="prerequisites"></a>Prérequis
Vérifiez que vous disposez des éléments suivants :

* Un compte Azure actif. Si vous n’en avez pas, vous pouvez vous inscrire pour bénéficier d’un [compte gratuit](https://azure.microsoft.com/free/). 
    * Vous pouvez également utiliser [l’émulateur local](local-emulator.md) pour ce didacticiel.
* [Visual Studio](http://www.visualstudio.com/).

## <a name="create-database-account"></a>Créer un compte de base de données

Commençons par créer un compte Azure Cosmos DB dans le portail Azure.  

> [!TIP]
> * Possédez-vous un compte Azure Cosmos DB ? Si tel est le cas, passez directement à l’étape [Configurer votre solution Visual Studio](#SetupVS).
> * Si vous utilisez l’émulateur Azure Cosmos DB, suivez les étapes de la section [Émulateur Azure Cosmos DB](local-emulator.md) pour le configurer, puis passez directement à l’étape [Configurer votre solution Visual Studio](#SetupVS). 
>
> 

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a id="SetupVS"></a>Configurer votre solution Visual Studio
1. Ouvrez **Visual Studio** sur votre ordinateur.
2. Dans le menu **Fichier**, sélectionnez **Nouveau**, puis choisissez **Projet**.
3. Dans la boîte de dialogue **Nouveau projet**, sélectionnez **Modèles** / **Visual C#** / **Application console (.NET Framework)**, donnez un nom à votre projet, puis cliquez sur **OK**.
4. Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur votre nouvelle application console, qui se trouve sous votre solution Visual Studio, puis cliquez sur **Gérer les packages NuGet…**
5. Dans l’onglet **NuGet** , cliquez sur **Parcourir**et tapez **Microsoft.Azure.Graphs** dans la zone de recherche et consultez l’article **Inclure les versions préliminaires**.
6. Dans les résultats, recherchez **Microsoft.Azure.Graphs** et cliquez sur **Installer**.
   
   Si vous obtenez un message concernant la vérification des modifications apportées à la solution, cliquez sur **OK**. Si vous obtenez un message concernant l’acceptation de la licence, cliquez sur **J’accepte**.
   
    La bibliothèque `Microsoft.Azure.Graphs` fournit une méthode d’extension unique `CreateGremlinQuery<T>` pour l’exécution d’opérations Gremlin. Gremlin est un langage de programmation fonctionnel qui prend en charge les opérations d’écriture (DML) et les opérations de requête et de traversée. Nous abordons quelques exemples dans cet article pour vous permettre de prendre en main Gremlin. [Requêtes Gremlin](gremlin-support.md) comporte une description détaillée des fonctionnalités de Gremlin disponibles dans Azure Cosmos DB.

## <a id="add-references"></a>Connecter votre application

Ajoutez ces deux constantes et votre variable *client* à votre application. 

```csharp
string endpoint = ConfigurationManager.AppSettings["Endpoint"]; 
string authKey = ConfigurationManager.AppSettings["AuthKey"]; 
``` 
Ensuite, revenez au [portail Azure](https://portal.azure.com) pour récupérer l’URL de votre point de terminaison et votre clé primaire. L’URL du point de terminaison et la clé primaire sont nécessaires pour que votre application sache où se connecter et qu’Azure Cosmos DB approuve la connexion de votre application. 

Dans le portail Azure, accédez à votre compte Azure Cosmos DB, cliquez sur **Clés**, puis sur **Clés en lecture-écriture**. 

Copiez l’URI à partir du portail et collez-le sur `Endpoint` dans la propriété du point de terminaison ci-dessus. Ensuite, copiez la clé primaire à partir du portail, puis collez-la dans la propriété `AuthKey` ci-dessus. 

![Capture d’écran du portail Azure utilisée par le didacticiel pour créer une application C#. Montre un compte Azure Cosmos DB, le bouton CLÉS mis en surbrillance dans le panneau de navigation Azure Cosmos DB et les valeurs d’URI et de CLÉ PRIMAIRE mises en surbrillance dans le volet Clés](./media/tutorial-develop-graph-dotnet/keys.png) 
 
## <a id="instantiate"></a>Instancier le DocumentClient 
Ensuite, créez une instance de **DocumentClient**.  

```csharp 
DocumentClient client = new DocumentClient(new Uri(endpoint), authKey); 
``` 

## <a id="create-database"></a>Créer une base de données 

Maintenant, créez une [base de données](sql-api-resources.md#databases) Azure Cosmos DB avec la méthode [CreateDatabaseAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdatabaseasync.aspx) ou la méthode [CreateDatabaseIfNotExistsAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdatabaseifnotexistsasync.aspx) de la classe **DocumentClient** provenant du [SDK .NET SQL](sql-api-sdk-dotnet.md).  

```csharp 
Database database = await client.CreateDatabaseIfNotExistsAsync(new Database { Id = "graphdb" }); 
``` 
 
## <a name="create-a-graph"></a>Créer un graphique 

Ensuite, créez un conteneur de graphiques à l’aide de la méthode [CreateDocumentCollectionAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdocumentcollectionasync.aspx) ou de la méthode [CreateDocumentCollectionIfNotExistsAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentcollectionifnotexistsasync.aspx) de la classe **DocumentClient**. Une collection est un conteneur d’entités graphiques. 

```csharp 
DocumentCollection graph = await client.CreateDocumentCollectionIfNotExistsAsync( 
    UriFactory.CreateDatabaseUri("graphdb"), 
    new DocumentCollection { Id = "graphcollz" }, 
    new RequestOptions { OfferThroughput = 1000 }); 
``` 

## <a id="serializing"></a>Sérialiser des vertex et des bords sur des objets .NET
Azure Cosmos DB utilise le [format de câble GraphSON](gremlin-support.md), qui définit un schéma JSON pour les vertex, les bords et les propriétés. Le Kit de développement logiciel (SDK) Azure Cosmos DB .NET inclut JSON.NET en tant que dépendance, et cela nous permet de sérialiser/désérialiser GraphSON dans des objets .NET avec lesquels nous pouvons travailler dans le code.

Par exemple, nous allons travailler avec un réseau social simple composé de quatre personnes. Nous examinons comment créer des vertex `Person`, ajouter des relations `Knows` entre eux, puis interroger et parcourir le graphique pour rechercher des relations « ami d’ami ». 

L’espace de noms `Microsoft.Azure.Graphs.Elements` fournit les classes `Vertex`, `Edge`, `Property` et `VertexProperty` pour la désérialisation de réponses GraphSON en objets .NET bien définis.

## <a name="run-gremlin-using-creategremlinquery"></a>Exécuter Gremlin à l’aide de CreateGremlinQuery
Gremlin, tout comme SQL, prend en charge les opérations de lecture, d’écriture et d’interrogation. Par exemple, l’extrait de code suivant montre comment créer des vertex, des bords, effectuer quelques exemples de requêtes à l’aide de `CreateGremlinQuery<T>`et itérer de manière asynchrone dans ces résultats à l’aide de `ExecuteNextAsync` et de `HasMoreResults.

```cs
Dictionary<string, string> gremlinQueries = new Dictionary<string, string>
{
    { "Cleanup",        "g.V().drop()" },
    { "AddVertex 1",    "g.addV('person').property('id', 'thomas').property('firstName', 'Thomas').property('age', 44)" },
    { "AddVertex 2",    "g.addV('person').property('id', 'mary').property('firstName', 'Mary').property('lastName', 'Andersen').property('age', 39)" },
    { "AddVertex 3",    "g.addV('person').property('id', 'ben').property('firstName', 'Ben').property('lastName', 'Miller')" },
    { "AddVertex 4",    "g.addV('person').property('id', 'robin').property('firstName', 'Robin').property('lastName', 'Wakefield')" },
    { "AddEdge 1",      "g.V('thomas').addE('knows').to(g.V('mary'))" },
    { "AddEdge 2",      "g.V('thomas').addE('knows').to(g.V('ben'))" },
    { "AddEdge 3",      "g.V('ben').addE('knows').to(g.V('robin'))" },
    { "UpdateVertex",   "g.V('thomas').property('age', 44)" },
    { "CountVertices",  "g.V().count()" },
    { "Filter Range",   "g.V().hasLabel('person').has('age', gt(40))" },
    { "Project",        "g.V().hasLabel('person').values('firstName')" },
    { "Sort",           "g.V().hasLabel('person').order().by('firstName', decr)" },
    { "Traverse",       "g.V('thomas').outE('knows').inV().hasLabel('person')" },
    { "Traverse 2x",    "g.V('thomas').outE('knows').inV().hasLabel('person').outE('knows').inV().hasLabel('person')" },
    { "Loop",           "g.V('thomas').repeat(out()).until(has('id', 'robin')).path()" },
    { "DropEdge",       "g.V('thomas').outE('knows').where(inV().has('id', 'mary')).drop()" },
    { "CountEdges",     "g.E().count()" },
    { "DropVertex",     "g.V('thomas').drop()" },
};

foreach (KeyValuePair<string, string> gremlinQuery in gremlinQueries)
{
    Console.WriteLine($"Running {gremlinQuery.Key}: {gremlinQuery.Value}");

    // The CreateGremlinQuery method extensions allow you to execute Gremlin queries and iterate
    // results asychronously
    IDocumentQuery<dynamic> query = client.CreateGremlinQuery<dynamic>(graph, gremlinQuery.Value);
    while (query.HasMoreResults)
    {
        foreach (dynamic result in await query.ExecuteNextAsync())
        {
            Console.WriteLine($"\t {JsonConvert.SerializeObject(result)}");
        }
    }

    Console.WriteLine();
}
```

## <a name="add-vertices-and-edges"></a>Ajouter des vertex et des bords

Nous allons examiner les instructions Gremlin indiquées dans la section précédente plus en détail. Nous commençons par créer des vertex à l’aide de la méthode `addV` de Gremlin. Par exemple, l’extrait de code suivant crée un vertex « Thomas Andersen » de type « Personne », avec des propriétés pour le prénom, le nom et l’âge.

```cs
// Create a vertex
IDocumentQuery<Vertex> createVertexQuery = client.CreateGremlinQuery<Vertex>(
    graphCollection, 
    "g.addV('person').property('firstName', 'Thomas')");

while (createVertexQuery.HasMoreResults)
{
    Vertex thomas = (await create.ExecuteNextAsync<Vertex>()).First();
}
```

Nous créons ensuite des bords entre ces vertex à l’aide de la méthode `addE` de Gremlin. 

```cs
// Add a "knows" edge
IDocumentQuery<Edge> createEdgeQuery = client.CreateGremlinQuery<Edge>(
    graphCollection, 
    "g.V('thomas').addE('knows').to(g.V('mary'))");

while (create.HasMoreResults)
{
    Edge thomasKnowsMaryEdge = (await create.ExecuteNextAsync<Edge>()).First();
}
```

Nous pouvons mettre à jour un vertex existant à l’aide de l’étape `properties` dans Gremlin. Nous ignorons l’appel pour exécuter la requête via `HasMoreResults` et `ExecuteNextAsync` pour le reste des exemples.

```cs
// Update a vertex
client.CreateGremlinQuery<Vertex>(
    graphCollection, 
    "g.V('thomas').property('age', 45)");
```

Vous pouvez supprimer des bords et des vertex en suivant l’étape `drop` de Gremlin. Voici un extrait de code qui montre comment supprimer un vertex et un bord. Notez que la suppression d’un vertex provoque une suppression en cascade des bords associés.

```cs
// Drop an edge
client.CreateGremlinQuery(graphCollection, "g.E('thomasKnowsRobin').drop()");

// Drop a vertex
client.CreateGremlinQuery(graphCollection, "g.V('robin').drop()");
```

## <a name="query-the-graph"></a>Interroger le graphique

Gremlin permet également d’effectuer des requêtes et des traversées. Par exemple, l’extrait de code suivant montre comment compter le nombre de vertex du graphique :

```cs
// Run a query to count vertices
IDocumentQuery<int> countQuery = client.CreateGremlinQuery<int>(graphCollection, "g.V().count()");
```
Vous pouvez appliquer des filtres à l’aide des étapes `has` et `hasLabel` de Gremlin et les associer à l’aide de `and`, `or`, et `not` pour créer des filtres plus complexes :

```cs
// Run a query with filter
IDocumentQuery<Vertex> personsByAge = client.CreateGremlinQuery<Vertex>(
  graphCollection, 
  "g.V().hasLabel('person').has('age', gt(40))");
```

Vous pouvez projeter certaines propriétés dans les résultats de requête à l’aide de l’étape `values` :

```cs
// Run a query with projection
IDocumentQuery<string> firstNames = client.CreateGremlinQuery<string>(
  graphCollection, 
  $"g.V().hasLabel('person').values('firstName')");
```

Jusqu’ici, nous avons seulement abordé les opérateurs de requête fonctionnant dans n’importe quelle base de données. Les graphiques sont rapides et efficaces pour les opérations de traversée lorsque vous avez besoin d’accéder aux vertex et bords associés. Recherchons à présent tous les amis de Thomas. Nous suivons pour cela l’étape `outE` de Gremlin pour rechercher toutes les bords externes de Thomas afin de se diriger vers les vertex internes de ces bords en suivant l’étape `inV` de Gremlin :

```cs
// Run a traversal (find friends of Thomas)
IDocumentQuery<Vertex> friendsOfThomas = client.CreateGremlinQuery<Vertex>(
  graphCollection,
  "g.V('thomas').outE('knows').inV().hasLabel('person')");
```

La requête suivante effectue deux sauts pour rechercher tous les « amis des amis de Thomas », en appelant `outE` et `inV` deux fois. 

```cs
// Run a traversal (find friends of friends of Thomas)
IDocumentQuery<Vertex> friendsOfFriendsOfThomas = client.CreateGremlinQuery<Vertex>(
  graphCollection,
  "g.V('thomas').outE('knows').inV().hasLabel('person').outE('knows').inV().hasLabel('person')");
```

Vous pouvez créer des requêtes plus complexes et implémenter une logique de traversée de graphique puissante à l’aide de Gremlin, y compris en mixant des expressions de filtre, en exécutant des boucles à l’aide de l’étape `loop` et en mettant en œuvre la navigation conditionnelle à l’aide de l’étape `choose`. En savoir plus sur ce que la [prise en charge de Gremlin](gremlin-support.md) vous permet de faire !

Ce didacticiel Azure Cosmos DB est maintenant terminé ! 

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous ne prévoyez pas de continuer à utiliser cette application, utilisez les étapes suivantes pour supprimer toutes les ressources créées par ce didacticiel dans le portail Azure.  

1. Dans le menu de gauche du portail Azure, cliquez sur **Groupes de ressources**, puis sur le nom de la ressource que vous avez créée. 
2. Sur la page de votre groupe de ressources, cliquez sur **Supprimer**, tapez le nom de la ressource à supprimer dans la zone de texte, puis cliquez sur **Supprimer**.

## <a name="next-steps"></a>étapes suivantes

Dans ce didacticiel, vous avez :

> [!div class="checklist"]
> * Créer un compte Azure Cosmos DB 
> * Créer une base de données des graphiques et un conteneur
> * Sérialiser des vertex et des bords sur des objets .NET
> * Ajouter des vertex et des bords
> * Interroger le graphique à l’aide de Gremlin

Vous pouvez maintenant générer des requêtes plus complexes et implémenter une logique de traversée de graphique puissante, à l’aide de Gremlin. 

> [!div class="nextstepaction"]
> [Interroger à l’aide de Gremlin](tutorial-query-graph.md)
