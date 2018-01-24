---
title: "Utilisation de données géospatiales dans Azure Cosmos DB | Microsoft Docs"
description: "Découvrez comment créer, indexer et interroger des objets spatiaux avec Azure Cosmos DB et l’API SQL."
services: cosmos-db
documentationcenter: 
author: arramac
manager: jhubbard
editor: monicar
ms.assetid: 82ce2898-a9f9-4acf-af4d-8ca4ba9c7b8f
ms.service: cosmos-db
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 10/20/2017
ms.author: arramac
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3e778f4a9b7ec4935d53eb335462f3c414ff99cd
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2017
---
# <a name="working-with-geospatial-and-geojson-location-data-in-azure-cosmos-db"></a>Utilisation de données d’emplacement géospatiales et GeoJSON dans Azure Cosmos DB
Cet article est une introduction aux fonctionnalités géospatiales dans [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/). Après avoir lu cet article, vous serez en mesure de répondre aux questions suivantes :

* Comment puis-je stocker des données spatiales dans Azure Cosmos DB ?
* Comment puis-je interroger des données géospaciales dans Azure Cosmos DB dans SQL et LINQ ?
* Comment puis-je activer ou désactiver l’indexation spatiale dans Azure Cosmos DB ?

Cet article montre comment utiliser les données spatiales avec l’API SQL. Consultez ce [projet GitHub](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Geospatial/Program.cs) pour obtenir des exemples de code.

## <a name="introduction-to-spatial-data"></a>Présentation des données spatiales
Les données spatiales décrivent la position et la forme des objets dans l'espace. Dans la plupart des applications, ils correspondent aux objets sur terre et aux données géospatiales. Les données spatiales peuvent servir à représenter l'emplacement d'une personne, d'un point d'intérêt ou de la limite d'une ville ou un lac. Les scénarios d’utilisation courants impliquent souvent des requêtes de proximité, comme « rechercher tous les cafés près de mon emplacement actuel ». 

### <a name="geojson"></a>GeoJSON
Azure Cosmos DB prend en charge l’indexation et l’interrogation des données de point géospatiales représentées à l’aide de la [spécification GeoJSON](https://tools.ietf.org/html/rfc7946). Les structures de données GeoJSON sont toujours des objets JSON valides, afin de pouvoir les stocker et les interroger à l’aide d’Azure Cosmos DB, sans bibliothèques ou outils spécialisés. Les kits SDK Azure Cosmos DB fournissent des classes d’assistance et des méthodes qui facilitent la manipulation des données spatiales. 

### <a name="points-linestrings-and-polygons"></a>Points, LineStrings et polygones
Un **point** désigne une position unique dans l'espace. Dans les données géospatiales, un point représente l’emplacement exact, qui peut être une adresse postale d’une épicerie, un kiosque, une voiture ou une ville.  Un point est représenté dans GeoJSON (et Azure Cosmos DB) à l’aide de sa paire de coordonnées ou de longitude et latitude. Voici un exemple JSON pour un point.

**Points dans Azure Cosmos DB**

```json
{
    "type":"Point",
    "coordinates":[ 31.9, -4.8 ]
}
```

> [!NOTE]
> La spécification GeoJSON spécifie la longitude d’abord, puis la latitude. Comme dans d'autres applications de mappage, la longitude et la latitude sont des angles et sont exprimées en degrés. Les valeurs de longitude sont mesurées à partir du premier méridien et sont comprises entre -180 degrés et 180 degrés. Les valeurs de latitude sont mesurées à partir de l’Équateur et sont comprises entre -90 degrés et 90 degrés. 
> 
> Azure Cosmos DB interprète les coordonnées représentées par le système de référence WGS-84. Voir ci-dessous pour plus d’informations sur les systèmes de coordonnées de référence.
> 
> 

Cela peut être incorporé dans un document Azure Cosmos DB, comme illustré dans cet exemple de profil utilisateur contenant des données d’emplacement :

**Utilisation de profil avec emplacement stocké dans Azure Cosmos DB**

```json
{
    "id":"cosmosdb-profile",
    "screen_name":"@CosmosDB",
    "city":"Redmond",
    "topics":[ "global", "distributed" ],
    "location":{
        "type":"Point",
        "coordinates":[ 31.9, -4.8 ]
    }
}
```

En plus des points, GeoJSON prend en charge les polygones et LineStrings. **LineStrings** représentent une série de deux ou plusieurs points dans l'espace et les segments de ligne qui les connectent. Dans les données géographiques, les LineStrings sont généralement utilisées pour représenter les autoroutes ou les cours d’eau. Un **polygone** est une limite de points reliés qui constitue une LineString fermée. Les polygones sont couramment utilisés pour représenter des formations naturelles comme des lacs ou des juridictions politiques comme les villes et les États. Voici un exemple de polygone dans Azure Cosmos DB. 

**Polygones dans GeoJSON**

```json
{
    "type":"Polygon",
    "coordinates":[ [
        [ 31.8, -5 ],
        [ 31.8, -4.7 ],
        [ 32, -4.7 ],
        [ 32, -5 ],
        [ 31.8, -5 ]
    ] ]
}
```

> [!NOTE]
> La spécification GeoJSON impose que, pour les polygones valides, la dernière paire de coordonnées fournie soit identique à la première, pour créer une forme fermée.
> 
> Les points dans un polygone doivent être spécifiés dans le sens antihoraire. Un polygone spécifié dans le sens horaire représente l’inverse de la région qu’il contient.
> 
> 

En plus des points, des LineStrings et des polygones, GeoJSON spécifie également la représentation pour le regroupement de plusieurs emplacements géospatiaux, ainsi que l’association de propriétés arbitraires avec la géolocalisation comme **fonctionnalité**. Étant donné que ces objets sont des JSON valides, ils peuvent tous être stockés et traités dans Azure Cosmos DB. Cependant, Azure Cosmos DB prend uniquement en charge l’indexation automatique des points.

### <a name="coordinate-reference-systems"></a>Coordination des systèmes de référence
Étant donné que la forme de la terre est irrégulière, les coordonnées des données géospatiales sont représentées dans de nombreux systèmes de coordonnées de référence (CRS), chacun ayant ses propres images de référence et unités de mesure. Par exemple, le « National Grid of Britain » est un système de référence très précis pour le Royaume-Uni, mais pas à l'extérieur. 

Le CRS moderne le plus populaire est le World Geodetic System [WGS-84](http://earth-info.nga.mil/GandG/wgs84/). Les périphériques GPS et de nombreux services de mappage, notamment les API Bing Maps et Google Maps, utilisent le WGS-84. Azure Cosmos DB prend en charge l’indexation et l’interrogation de données géographiques uniquement à l’aide du CRS WGS-84. 

## <a name="creating-documents-with-spatial-data"></a>Création de documents avec les données spatiales
Lorsque vous créez des documents qui contiennent des valeurs GeoJSON, ils sont automatiquement indexés avec un index spatial conformément à la stratégie d'indexation de la collection. Si vous travaillez avec un Kit de développement logiciel (SDK) Azure Cosmos DB dans un langage saisi dynamiquement comme Python ou Node.js, vous devez créer un GeoJSON valide.

**Création d’un document avec les données géographiques dans Node.js**

```json
var userProfileDocument = {
    "name":"cosmosdb",
    "location":{
        "type":"Point",
        "coordinates":[ -122.12, 47.66 ]
    }
};

client.createDocument(`dbs/${databaseName}/colls/${collectionName}`, userProfileDocument, (err, created) => {
    // additional code within the callback
});
```

Si vous travaillez avec les API SQL, vous pouvez utiliser les classes `Point` et `Polygon` dans l’espace de noms `Microsoft.Azure.Documents.Spatial` pour incorporer des informations d’emplacement au sein des objets de votre application. Ces classes permettent de simplifier la sérialisation et la désérialisation de données spatiales dans GeoJSON.

**Création d’un document avec les données géographiques dans .NET**

```json
using Microsoft.Azure.Documents.Spatial;

public class UserProfile
{
    [JsonProperty("name")]
    public string Name { get; set; }

    [JsonProperty("location")]
    public Point Location { get; set; }

    // More properties
}

await client.CreateDocumentAsync(
    UriFactory.CreateDocumentCollectionUri("db", "profiles"), 
    new UserProfile 
    { 
        Name = "cosmosdb", 
        Location = new Point (-122.12, 47.66) 
    });
```

Si vous n'avez pas les informations de latitude et de longitude, mais disposez des adresses physiques ou du nom d'emplacement comme la ville ou le pays, vous pouvez rechercher les coordonnées réelles à l'aide d'un service de géocodage comme Bing Maps REST Services. En savoir plus sur le géocodage de Bing Maps [ici](https://msdn.microsoft.com/library/ff701713.aspx).

## <a name="querying-spatial-types"></a>Interrogation des types spatiaux
Maintenant que nous avons vu comment insérer des données géospatiales, voyons comment interroger ces données à l’aide d’Azure Cosmos DB avec SQL et LINQ.

### <a name="spatial-sql-built-in-functions"></a>Fonctions spatiales SQL intégrées
Azure Cosmos DB prend en charge les fonctions intégrées Open Geospatial Consortium (OGC) suivantes pour les requêtes géospatiales. Pour plus d’informations sur l’ensemble complet de fonctions intégrées dans le langage SQL, consultez [Requête Azure Cosmos DB](sql-api-sql-query.md).

<table>
<tr>
  <td><strong>Utilisation</strong></td>
  <td><strong>Description</strong></td>
</tr>
<tr>
  <td>ST_DISTANCE (spatial_expr, spatial_expr)</td>
  <td>Retourne la distance entre les deux expressions Point, Polygone ou LineString de GeoJSON.</td>
</tr>
<tr>
  <td>ST_WITHIN (spatial_expr, spatial_expr)</td>
  <td>Retourne une expression booléenne indiquant si le premier objet GeoJSON (Point, Polygone ou LineString) se trouve dans le second objet GeoJSON (Point, Polygone ou LineString).</td>
</tr>
<tr>
  <td>ST_INTERSECTS (spatial_expr, spatial_expr)</td>
  <td>Retourne une expression booléenne indiquant si les deux objets GeoJSON spécifiés (Point, Polygone ou LineString) se croisent.</td>
</tr>
<tr>
  <td>ST_ISVALID</td>
  <td>Renvoie une valeur booléenne indiquant si l’expression Point, Polygone ou LineString GeoJSON spécifiée est valide.</td>
</tr>
<tr>
  <td>ST_ISVALIDDETAILED</td>
  <td>Renvoie une valeur JSON contenant une valeur booléenne si l’expression Point, Polygone ou LineString GeoJSON spécifiée est valide et, dans le cas contraire, le motif sous forme de valeur de chaîne.</td>
</tr>
</table>

Les fonctions spatiales peuvent être utilisées pour effectuer des requêtes de proximité par rapport aux données spatiales. Par exemple, voici une requête qui retourne tous les documents de famille se trouvant dans un rayon de 30 kilomètres de l'emplacement spécifié à l'aide de la fonction intégrée ST_DISTANCE. 

**Requête**

    SELECT f.id 
    FROM Families f 
    WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000

**Résultats**

    [{
      "id": "WakefieldFamily"
    }]

Si vous incluez l'indexation spatiale dans votre stratégie d'indexation, les « requêtes à distance » seront servies efficacement dans l'index. Pour plus d’informations sur l’indexation spatiale, consultez la section ci-dessous. Si vous n'avez pas un index spatial pour les chemins d'accès spécifiés, vous pouvez quand même effectuer des requêtes spatiales en spécifiant l’en-tête de requête `x-ms-documentdb-query-enable-scan` avec la valeur définie sur « true ». Dans .NET, cela est possible en passant l’argument facultatif **FeedOptions** aux requêtes avec [EnableScanInQuery](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.enablescaninquery.aspx#P:Microsoft.Azure.Documents.Client.FeedOptions.EnableScanInQuery) défini sur true. 

Vous pouvez utiliser ST_WITHIN pour vérifier si un point se trouve dans un polygone. Généralement, les polygones sont utilisés pour représenter des limites comme les codes postaux, les frontières d’États ou les formations naturelles. Si vous incluez l'indexation spatiale dans votre stratégie d'indexation, les requêtes « within » seront servies efficacement dans l'index. 

Les arguments de polygone dans ST_WITHIN peuvent contenir un seul cercle. Cela signifie que les polygones ne doivent pas contenir de trous. 

**Requête**

    SELECT * 
    FROM Families f 
    WHERE ST_WITHIN(f.location, {
        'type':'Polygon', 
        'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
    })

**Résultats**

    [{
      "id": "WakefieldFamily",
    }]

> [!NOTE]
> Tout comme pour les types non correspondants dans une requête Azure Cosmos DB, si la valeur de l’emplacement spécifié dans un argument est incorrecte ou non valide, elle prend alors la valeur **indéfinie** et le document évalué est ignoré des résultats de requête. Si votre requête ne retourne aucun résultat, exécutez ST_ISVALIDDETAILED afin de déboguer l’absence de validité du type spatial.     
> 
> 

Azure Cosmos DB prend également en charge les requêtes inversées. Vous pouvez, par exemple, indexer des polygones ou des lignes dans Azure Cosmos DB, puis interroger les zones qui contiennent un point spécifique. Ce modèle est généralement utilisé dans la logistique pour identifier quand un camion entre ou quitte une région spécifiée, par exemple. 

**Requête**

    SELECT * 
    FROM Areas a 
    WHERE ST_WITHIN({'type': 'Point', 'coordinates':[31.9, -4.8]}, a.location)


**Résultats**

    [{
      "id": "MyDesignatedLocation",
      "location": {
        "type":"Polygon", 
        "coordinates": [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
      }
    }]

ST_ISVALID et ST_ISVALIDDETAILED peuvent être utilisés pour vérifier si un objet spatial est valide. Par exemple, la requête suivante vérifie la validité d'un point avec une valeur de latitude hors limites (-132.8). ST_ISVALID retourne simplement une valeur booléenne et ST_ISVALIDDETAILED renvoie la valeur booléenne et une chaîne contenant la raison pour laquelle il est non valide.

** Requête **

    SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] })

**Résultats**

    [{
      "$1": false
    }]

Vous pouvez aussi utiliser ces fonctions pour valider des polygones. Par exemple, nous utilisons ici ST_ISVALIDDETAILED pour valider un polygone non fermé. 

**Requête**

    SELECT ST_ISVALIDDETAILED({ "type": "Polygon", "coordinates": [[ 
        [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] 
        ]]})

**Résultats**

    [{
       "$1": { 
            "valid": false, 
            "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a Polygon must have the same start and end points." 
          }
    }]

### <a name="linq-querying-in-the-net-sdk"></a>Interrogation LINQ dans le Kit de développement logiciel (SDK) .NET
Le SDK .NET SQL fournit également les méthodes de stub `Distance()` et `Within()` pour une utilisation dans des expressions LINQ. Le fournisseur SQL LINQ traduit ces appels de méthode pour les appels de fonction intégrés SQL équivalents (ST_DISTANCE et ST_WITHIN, respectivement). 

Voici un exemple de requête LINQ qui recherche tous les documents de la collection Azure Cosmos DB dont la valeur « location » est dans un rayon de 30 kilomètres du point spécifié à l’aide de LINQ.

**Requête LINQ de distance**

    foreach (UserProfile user in client.CreateDocumentQuery<UserProfile>(UriFactory.CreateDocumentCollectionUri("db", "profiles"))
        .Where(u => u.ProfileType == "Public" && a.Location.Distance(new Point(32.33, -4.66)) < 30000))
    {
        Console.WriteLine("\t" + user);
    }

De même, voici une requête pour rechercher tous les documents dont la « location » est dans la zone/le polygone spécifié. 

**Requête LINQ within**

    Polygon rectangularArea = new Polygon(
        new[]
        {
            new LinearRing(new [] {
                new Position(31.8, -5),
                new Position(32, -5),
                new Position(32, -4.7),
                new Position(31.8, -4.7),
                new Position(31.8, -5)
            })
        });

    foreach (UserProfile user in client.CreateDocumentQuery<UserProfile>(UriFactory.CreateDocumentCollectionUri("db", "profiles"))
        .Where(a => a.Location.Within(rectangularArea)))
    {
        Console.WriteLine("\t" + user);
    }


Maintenant que nous avons expliqué l’interrogation de documents à l’aide de LINQ et SQL, voyons comment configurer Azure Cosmos DB pour l’indexation spatiale.

## <a name="indexing"></a>Indexation
Comme décrit dans le livre [Schema Agnostic Indexing with Azure Cosmos DB](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf), nous avons conçu le moteur de base de données Azure Cosmos DB pour être véritablement indépendant du schéma et fournir une assistance exceptionnelle pour JSON. Le moteur de base de données optimisé en écriture d’Azure Cosmos DB comprend les données spatiales (points, polygones et lignes) représentées dans la norme GeoJSON en mode natif.

En bref, la géométrie est projetée à partir des coordonnées géodésiques sur un plan 2D, puis divisée progressivement en cellules à l'aide un **quadtree**. Ces cellules sont mappées en 1D selon l'emplacement de la cellule dans une **courbe de remplissage d'espace de Hilbert**qui permet de préserver la localité des points. En outre, quand les données d’emplacement sont indexées, elles passent par un processus connu sous le nom de **pavage**, c’est-à-dire que toutes les cellules qui se croisent à un emplacement sont identifiées et stockées en tant que clés dans l’index Azure Cosmos DB. Au moment de la requête, des arguments comme les points et les polygones sont également fractionnés pour extraire les plages d’ID de cellule appropriées, puis utilisés pour récupérer des données à partir de l’index.

Si vous spécifiez une stratégie d'indexation qui inclut un index spatial pour /* (tous les chemins d'accès), tous les points trouvés dans la collection sont indexés pour des requêtes spatiales efficaces (ST_WITHIN et ST_DISTANCE). Les index spatiaux n'ont pas une valeur de précision et utilisent toujours une valeur de précision par défaut.

> [!NOTE]
> Azure Cosmos DB prend en charge l’indexation automatique des points, polygones et lineStrings.
> 
> 

L’extrait JSON suivant montre une politique d’indexation avec l’indexation spatiale activée, c’est-à-dire indexer n’importe quel point GeoJSON trouvé au sein de documents pour l’interrogation spatiale. Si vous modifiez la stratégie d’indexation à l’aide du portail Azure, vous pouvez spécifier le JSON suivant pour la stratégie d’indexation pour activer l’indexation spatiale pour votre collection.

**Stratégie d’indexation de collection JSON avec Spatial activé pour les points et les polygones**

    {
       "automatic":true,
       "indexingMode":"Consistent",
       "includedPaths":[
          {
             "path":"/*",
             "indexes":[
                {
                   "kind":"Range",
                   "dataType":"String",
                   "precision":-1
                },
                {
                   "kind":"Range",
                   "dataType":"Number",
                   "precision":-1
                },
                {
                   "kind":"Spatial",
                   "dataType":"Point"
                },
                {
                   "kind":"Spatial",
                   "dataType":"Polygon"
                }                
             ]
          }
       ],
       "excludedPaths":[
       ]
    }

Voici un extrait de code dans .NET qui montre comment créer une collection avec l’indexation spatiale activée pour tous les chemins d'accès contenant des points. 

**Création d’une collection avec l'indexation spatiale**

    DocumentCollection spatialData = new DocumentCollection()
    spatialData.IndexingPolicy = new IndexingPolicy(new SpatialIndex(DataType.Point)); //override to turn spatial on by default
    collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), spatialData);

Voici comment vous pouvez modifier un regroupement existant pour tirer parti de l'indexation spatiale sur tous les points stockés au sein de documents.

**Modification d’une collection existante avec l'indexation spatiale**

    Console.WriteLine("Updating collection with spatial indexing enabled in indexing policy...");
    collection.IndexingPolicy = new IndexingPolicy(new SpatialIndex(DataType.Point));
    await client.ReplaceDocumentCollectionAsync(collection);

    Console.WriteLine("Waiting for indexing to complete...");
    long indexTransformationProgress = 0;
    while (indexTransformationProgress < 100)
    {
        ResourceResponse<DocumentCollection> response = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));
        indexTransformationProgress = response.IndexTransformationProgress;

        await Task.Delay(TimeSpan.FromSeconds(1));
    }

> [!NOTE]
> Si la valeur GeoJSON d'emplacement dans le document est incorrecte ou non valide, elle n’est pas indexée pour les requêtes spatiales. Vous pouvez valider les valeurs d'emplacement à l'aide de ST_ISVALID et ST_ISVALIDDETAILED.
> 
> Si la définition de votre collection contient une clé de partition, la progression de la transformation d’indexation n’est pas signalée. 
> 
> 

## <a name="next-steps"></a>étapes suivantes
Maintenant que vous avez appris à utiliser la prise en charge géospatiale dans Azure Cosmos DB, vous pouvez :

* Commencer à coder avec les [exemples de code .NET Geospatial sur GitHub](https://github.com/Azure/azure-documentdb-dotnet/blob/fcf23d134fc5019397dcf7ab97d8d6456cd94820/samples/code-samples/Geospatial/Program.cs)
* Découvrir l’interrogation géospatiale dans [Azure Cosmos DB Query Playground](http://www.documentdb.com/sql/demo#geospatial)
* En savoir plus sur les [requêtes Azure Cosmos DB](sql-api-sql-query.md)
* En savoir plus sur les [stratégies d’indexation Azure Cosmos DB](indexing-policies.md)

