---
title: Interroger un index (API .NET - Recherche Azure) | Microsoft Docs
description: "Créez une requête de recherche dans Azure Search et utilisez des paramètres de recherche pour filtrer et trier les résultats de recherche."
services: search
manager: jhubbard
documentationcenter: 
author: brjohnstmsft
ms.assetid: 12c3efba-ea99-4187-9d2d-f63b5ec7040d
ms.service: search
ms.devlang: dotnet
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 05/19/2017
ms.author: brjohnst
ms.openlocfilehash: c3c22b83346269cf3c0327fe3fb98510a6266733
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
# <a name="query-your-azure-search-index-using-the-net-sdk"></a>Interroger un index Azure Search à l’aide du Kit de développement logiciel (SDK) .NET
> [!div class="op_single_selector"]
> * [Vue d'ensemble](search-query-overview.md)
> * [Portail](search-explorer.md)
> * [.NET](search-query-dotnet.md)
> * [REST](search-query-rest-api.md)
> 
> 

Cet article explique comment interroger un index à l’aide du [Kit de développement logiciel (SDK) .NET Azure Search](https://aka.ms/search-sdk).

Avant de commencer cette procédure, vous devez déjà avoir [créé un index Azure Search](search-what-is-an-index.md) et y avoir [ajouté des données](search-what-is-data-import.md).

> [!NOTE]
> Tous les exemples de code figurant dans cet article sont écrits en C#. L’intégralité du code source est disponible [sur GitHub](http://aka.ms/search-dotnet-howto). Vous pouvez également consulter le [kit de développement logiciel (SDK) .NET Azure Search](search-howto-dotnet-sdk.md) pour une description plus détaillée de l’exemple de code.

## <a name="identify-your-azure-search-services-query-api-key"></a>Identifier la clé API de requête de votre service Azure Search
Maintenant que vous avez créé un index Azure Search, vous êtes presque prêt à générer des requêtes à l’aide du Kit de développement logiciel (SDK) .NET. Tout d’abord, vous devez obtenir l’une des clés API de requête qui a été générée pour le service de recherche que vous avez configuré. À chaque demande, le Kit de développement logiciel (SDK) .NET envoie la clé API à votre service. L’utilisation d’une clé valide permet d’établir, en fonction de chaque demande, une relation de confiance entre l’application qui envoie la demande et le service qui en assure le traitement.

1. Pour accéder aux clés API de votre service, vous pouvez vous connecter au [portail Azure](https://portal.azure.com/)
2. Accédez au panneau de votre service Azure Search
3. Cliquez sur l’icône « Clés »

Votre service comporte à la fois des *clés d’administration* et des *clés de requête*.

* Les *clés d’administration* principales et secondaires vous accordent des droits d’accès complets à toutes les opérations, avec notamment la possibilité de gérer le service ou de créer et supprimer des index, des indexeurs et des sources de données. Deux clés sont à votre disposition afin que vous puissiez continuer à utiliser la clé secondaire si vous décidez de régénérer la clé primaire et inversement.
* Vos *clés de requête* vous accordent un accès en lecture seule aux index et documents ; elles sont généralement distribuées aux applications clientes qui émettent des demandes de recherche.

Dans le cadre de l’interrogation d’un index, vous pouvez utiliser l’une de vos clés de requête. Vos clés d’administration peuvent également vous servir pour exécuter des requêtes, mais il est recommandé d’utiliser une clé de requête dans votre code d’application, car cette approche respecte davantage le [principe du moindre privilège](https://en.wikipedia.org/wiki/Principle_of_least_privilege).

## <a name="create-an-instance-of-the-searchindexclient-class"></a>Créer une instance de la classe SearchIndexClient
Pour générer des requêtes avec le Kit de développement logiciel (SDK) .NET Azure Search, vous devez créer une instance de la classe `SearchIndexClient` . Cette classe dispose de plusieurs constructeurs. Celui qui vous intéresse prend le nom du service de recherche, le nom de l’index et un objet `SearchCredentials` en tant que paramètres. `SearchCredentials` encapsule votre clé API.

Le code ci-dessous génère un nouveau `SearchIndexClient` pour les index « hotels » (créés dans [Création d’un index Azure Search à l’aide du Kit de développement logiciel (SDK) .NET](search-create-index-dotnet.md)) en utilisant les valeurs du nom du service de recherche et de la clé API, stockées dans le fichier de configuration de l’application (`appsettings.json` dans le cas de [l’exemple d’application](http://aka.ms/search-dotnet-howto)) :

```csharp
private static SearchIndexClient CreateSearchIndexClient(IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string queryApiKey = configuration["SearchServiceQueryApiKey"];

    SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, "hotels", new SearchCredentials(queryApiKey));
    return indexClient;
}
```

`SearchIndexClient` a une propriété `Documents`. Cette propriété fournit toutes les méthodes dont vous avez besoin pour interroger les index Azure Search.

## <a name="query-your-index"></a>Interroger votre index
La recherche à l’aide du Kit de développement logiciel (SDK) .NET est aussi simple que l’appel de la méthode `Documents.Search` sur votre `SearchIndexClient`. Cette méthode accepte quelques paramètres, notamment le texte de recherche, avec un objet `SearchParameters` qui peut être utilisé pour affiner la requête.

#### <a name="types-of-queries"></a>Types de requête
Les deux principaux [types de requête](search-query-overview.md#types-of-queries) que vous utilisez sont `search` et `filter`. Une requête `search` recherche un ou plusieurs termes dans tous les champs de *recherche* de votre index. Une requête `filter` permet d’évaluer une expression booléenne dans tous les champs *filtrables* d’un index. Vous pouvez utiliser des recherches et des filtres conjointement ou séparément.

Les filtres et les recherches sont exécutés à l’aide de la méthode `Documents.Search` . Une requête de recherche peut être transmise dans le paramètre `searchText`, tandis qu’une expression de filtre peut être transmise dans la propriété `Filter` de la classe `SearchParameters`. Pour filtrer sans effectuer de recherche, transmettez simplement `"*"` pour le paramètre `searchText`. Pour effectuer une recherche sans appliquer de filtre, ne définissez pas la propriété `Filter` et ne transmettez pas une instance `SearchParameters`.

#### <a name="example-queries"></a>Exemples de requêtes
L’exemple de code suivant montre différentes manières d’interroger l’index « hotels » défini dans [Créer un index Azure Search à l’aide du Kit de développement logiciel (SDK) .NET](search-create-index-dotnet.md#DefineIndex). Notez que les documents renvoyés avec les résultats de recherche sont des instances de la classe `Hotel` , qui a été définie dans [Importer des données dans Azure Search à l’aide du Kit de développement logiciel (SDK) .NET](search-import-data-dotnet.md#HotelClass). L’exemple de code utilise une méthode `WriteDocuments` pour générer les résultats de recherche dans la console. Cette méthode est décrite dans la section suivante.

```csharp
SearchParameters parameters;
DocumentSearchResult<Hotel> results;

Console.WriteLine("Search the entire index for the term 'budget' and return only the hotelName field:\n");

parameters =
    new SearchParameters()
    {
        Select = new[] { "hotelName" }
    };

results = indexClient.Documents.Search<Hotel>("budget", parameters);

WriteDocuments(results);

Console.Write("Apply a filter to the index to find hotels cheaper than $150 per night, ");
Console.WriteLine("and return the hotelId and description:\n");

parameters =
    new SearchParameters()
    {
        Filter = "baseRate lt 150",
        Select = new[] { "hotelId", "description" }
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);

Console.Write("Search the entire index, order by a specific field (lastRenovationDate) ");
Console.Write("in descending order, take the top two results, and show only hotelName and ");
Console.WriteLine("lastRenovationDate:\n");

parameters =
    new SearchParameters()
    {
        OrderBy = new[] { "lastRenovationDate desc" },
        Select = new[] { "hotelName", "lastRenovationDate" },
        Top = 2
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);

Console.WriteLine("Search the entire index for the term 'motel':\n");

parameters = new SearchParameters();
results = indexClient.Documents.Search<Hotel>("motel", parameters);

WriteDocuments(results);
```

## <a name="handle-search-results"></a>Traiter les résultats de recherche
La méthode `Documents.Search` renvoie un objet `DocumentSearchResult` qui contient les résultats de la requête. L’exemple figurant dans la section précédente utilisait une méthode appelée `WriteDocuments` pour générer les résultats de recherche dans la console :

```csharp
private static void WriteDocuments(DocumentSearchResult<Hotel> searchResults)
{
    foreach (SearchResult<Hotel> result in searchResults.Results)
    {
        Console.WriteLine(result.Document);
    }

    Console.WriteLine();
}
```

Voici les résultats des requêtes figurant dans la section précédente, en supposant que l’index « hotels » est rempli à l’aide des exemples de données figurant dans [Importer des données dans Azure Search à l’aide du Kit de développement logiciel (SDK) .NET](search-import-data-dotnet.md):

```
Search the entire index for the term 'budget' and return only the hotelName field:

Name: Roach Motel

Apply a filter to the index to find hotels cheaper than $150 per night, and return the hotelId and description:

ID: 2   Description: Cheapest hotel in town
ID: 3   Description: Close to town hall and the river

Search the entire index, order by a specific field (lastRenovationDate) in descending order, take the top two results, and show only hotelName and lastRenovationDate:

Name: Fancy Stay        Last renovated on: 6/27/2010 12:00:00 AM +00:00
Name: Roach Motel       Last renovated on: 4/28/1982 12:00:00 AM +00:00

Search the entire index for the term 'motel':

ID: 2   Base rate: 79.99        Description: Cheapest hotel in town     Description (French): Hôtel le moins cher en ville      Name: Roach Motel       Category: Budget        Tags: [motel, budget]   Parking included: yes   Smoking allowed: yes    Last renovated on: 4/28/1982 12:00:00 AM +00:00 Rating: 1/5     Location: Latitude 49.678581, longitude -122.131577
```

L’exemple de code ci-dessus utilise la console pour générer les résultats de recherche. De même, vous devez afficher les résultats de recherche dans votre propre application. Consultez [cet exemple sur GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetSample) pour obtenir un exemple illustrant l’affichage des résultats de recherche dans une application web ASP.NET MVC.

