---
title: "Créer un index (API .NET - Recherche Azure) | Microsoft Docs"
description: "Créer un index dans le code à l’aide du kit de développement logiciel (SDK) .NET d’Azure Search."
services: search
documentationcenter: 
author: brjohnstmsft
manager: jhubbard
editor: 
tags: azure-portal
ms.assetid: 3a851647-fc7b-4fb6-8506-6aaa519e77cd
ms.service: search
ms.devlang: dotnet
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 05/22/2017
ms.author: brjohnst
ms.openlocfilehash: fac41903c3e5731d17f832ff58145fe74dfa29f1
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/18/2017
---
# <a name="create-an-azure-search-index-using-the-net-sdk"></a>Création d’un index Azure Search à l’aide du Kit de développement logiciel (SDK) .NET
> [!div class="op_single_selector"]
> * [Vue d'ensemble](search-what-is-an-index.md)
> * [Portail](search-create-index-portal.md)
> * [.NET](search-create-index-dotnet.md)
> * [REST](search-create-index-rest-api.md)
> 
> 

Cet article vous guidera dans la création d’un [index](https://docs.microsoft.com/rest/api/searchservice/Create-Index) Azure Search à l’aide du [kit de développement logiciel (SDK) .NET Azure Search](https://aka.ms/search-sdk).

Avant de suivre ce guide et de passer à la création d’un index, vous devez avoir déjà [créé un service Azure Search](search-create-service-portal.md).

> [!NOTE]
> Tous les exemples de code figurant dans cet article sont écrits en C#. L’intégralité du code source est disponible [sur GitHub](http://aka.ms/search-dotnet-howto). Vous pouvez également consulter le [kit de développement logiciel (SDK) .NET Azure Search](search-howto-dotnet-sdk.md) pour une description plus détaillée de l’exemple de code.


## <a name="identify-your-azure-search-services-admin-api-key"></a>Identifier la clé API d’administration de votre service Azure Search
Maintenant que vous avez configuré un service Azure Search, vous être presque prêt à émettre des requêtes sur le système d’extrémité de votre service à l’aide du kit de développement logiciel (SDK) .NET. Tout d'abord, vous devez obtenir l’une des clés API d'administration générées pour le service Search que vous avez configuré. Le kit de développement logiciel (SDK) .NET envoie la clé API à chaque demande à votre service. L’utilisation d’une clé valide permet d’établir, en fonction de chaque demande, une relation de confiance entre l’application qui envoie la demande et le service qui en assure le traitement.

1. Pour accéder aux clés API de votre service, connectez-vous au [portail Azure](https://portal.azure.com/)
2. Accédez au panneau de votre service Azure Search
3. Cliquez sur l’icône « Clés »

Votre service comporte à la fois des *clés d’administration* et des *clés de requête*.

* Les *clés d’administration* principales et secondaires vous accordent des droits d’accès complets à toutes les opérations, avec notamment la possibilité de gérer le service ou de créer et supprimer des index, des indexeurs et des sources de données. Deux clés sont à votre disposition afin que vous puissiez continuer à utiliser la clé secondaire si vous décidez de régénérer la clé primaire et inversement.
* Vos *clés de requête* vous accordent un accès en lecture seule aux index et documents ; elles sont généralement distribuées aux applications clientes qui émettent des demandes de recherche.

Dans le cadre de la création d’un index, vous pouvez utiliser votre clé d’administration principale ou secondaire.

<a name="CreateSearchServiceClient"></a>

## <a name="create-an-instance-of-the-searchserviceclient-class"></a>Création d’une instance de la classe SearchServiceClient
Pour commencer à utiliser le Kit de développement logiciel (SDK) .NET Azure Search, vous devez créer une instance de la classe `SearchServiceClient` . Cette classe dispose de plusieurs constructeurs. Celle que vous cherchez utilise le nom de votre service de recherche et un objet `SearchCredentials` en tant paramètres. `SearchCredentials` encapsule votre clé API.

Le code suivant crée un nouveau `SearchServiceClient` à l’aide de valeurs pour le nom du service de recherche et la clé API stockés dans le fichier de configuration de l’application (`appsettings.json` ou le cas de l’[exemple d’application](http://aka.ms/search-dotnet-howto)) :

```csharp
private static SearchServiceClient CreateSearchServiceClient(IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string adminApiKey = configuration["SearchServiceAdminApiKey"];

    SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
    return serviceClient;
}
```

`SearchServiceClient` a une propriété `Indexes`. Cette propriété fournit toutes les méthodes dont vous avez besoin pour créer, afficher, mettre à jour ou supprimer des index Azure Search.

> [!NOTE]
> La classe `SearchServiceClient` gère les connexions à votre service de recherche. Pour éviter l'ouverture d'un trop grand nombre de connexions, essayez de partager une seule instance de `SearchServiceClient` dans votre application, si possible. Ses méthodes sont thread-safe pour activer le partage.
> 
> 

<a name="DefineIndex"></a>

## <a name="define-your-azure-search-index"></a>Définir votre index Recherche Azure
Un seul appel à la méthode `Indexes.Create` crée votre index. Cette méthode prend comme paramètre un objet `Index` qui définit votre index Azure Search. Vous devez créer un objet `Index` et l'initialiser comme suit :

1. Définissez la propriété `Name` de l’objet `Index` sur le nom de votre index.
2. Définissez la propriété `Fields` de l’objet `Index` sur l’array d’objets `Field`. Le moyen le plus simple de créer les objets `Field` consiste à appeler la méthode `FieldBuilder.BuildForType` en transmettant une classe de modèle pour le paramètre de type. Une classe de modèle comporte des propriétés qui sont mappées sur les champs de votre index. Cela vous permet de lier des documents à partir de votre index de recherche à des instances de votre classe de modèle.

> [!NOTE]
> Si vous n’envisagez pas d’utiliser une classe de modèle, vous pouvez toujours définir votre index en créant les objets `Field` directement. Vous pouvez fournir le nom du champ au constructeur, ainsi que le type de données (ou un analyseur pour les champs de chaîne). Vous pouvez également définir d'autres propriétés comme `IsSearchable`, `IsFilterable`, etc.
>
>

Il est important de ne perdre de vue ni votre expérience de recherche ni vos besoins métiers lorsque vous concevez votre index, étant donné que vous devez affecter les [propriétés appropriées](https://docs.microsoft.com/rest/api/searchservice/Create-Index) à chaque champ. Ces propriétés déterminent les fonctionnalités de recherche (filtrage, facettes, tri de recherche en texte intégral, etc.) qui s’appliqueront à chaque champ. Pour les propriétés que vous ne définissez pas explicitement, la classe `Field` désactive par défaut la fonctionnalité de recherche correspondante, sauf si vous l'activez de façon spécifique.

Dans notre exemple, nous avons nommé notre index « hotels » et nous avons défini nos champs à l’aide d’une classe de modèle. Chaque propriété de la classe de modèle comporte des attributs qui déterminent les comportements liés à la recherche du champ d’index correspondant. La classe de modèle est définie comme suit :

```csharp
using System;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Microsoft.Spatial;
using Newtonsoft.Json;

// The SerializePropertyNamesAsCamelCase attribute is defined in the Azure Search .NET SDK.
// It ensures that Pascal-case property names in the model class are mapped to camel-case
// field names in the index.
[SerializePropertyNamesAsCamelCase]
public partial class Hotel
{
    [System.ComponentModel.DataAnnotations.Key]
    [IsFilterable]
    public string HotelId { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public double? BaseRate { get; set; }

    [IsSearchable]
    public string Description { get; set; }

    [IsSearchable]
    [Analyzer(AnalyzerName.AsString.FrLucene)]
    [JsonProperty("description_fr")]
    public string DescriptionFr { get; set; }

    [IsSearchable, IsFilterable, IsSortable]
    public string HotelName { get; set; }

    [IsSearchable, IsFilterable, IsSortable, IsFacetable]
    public string Category { get; set; }

    [IsSearchable, IsFilterable, IsFacetable]
    public string[] Tags { get; set; }

    [IsFilterable, IsFacetable]
    public bool? ParkingIncluded { get; set; }

    [IsFilterable, IsFacetable]
    public bool? SmokingAllowed { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public DateTimeOffset? LastRenovationDate { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public int? Rating { get; set; }

    [IsFilterable, IsSortable]
    public GeographyPoint Location { get; set; }
}
```

Nous avons soigneusement choisi les attributs de chaque propriété en fonction de la manière dont ils seront vraisemblablement utilisés dans une application. Par exemple, il est probable que les personnes recherchant des hôtels seront intéressées par les correspondances de mots-clés sur le champ `description`. Ainsi, nous activons la recherche en texte intégral pour ce champ en définissant l’attribut `IsSearchable` sur la propriété `Description`.

Notez que, dans votre index, vous ne devez désigner qu’un seul champ de type `string` en tant que champ *clé* en ajoutant l’attribut `Key` (voir `HotelId` dans l’exemple ci-dessus).

La définition d’index ci-dessus utilise un analyseur de langue pour le champ `description_fr` dans la mesure où il est destiné à stocker du texte en français. Pour plus d’informations sur les analyseurs de langue, consultez [l’article relatif à la prise en charge linguistique](https://docs.microsoft.com/rest/api/searchservice/Language-support), ainsi que le [billet de blog](https://azure.microsoft.com/blog/language-support-in-azure-search/) correspondant.

> [!NOTE]
> Par défaut, le nom de chaque propriété de votre classe de modèle est utilisé comme nom du champ correspondant dans l’index. Si vous souhaitez mapper tous les noms de propriété sur les noms de champ en casse mixte, marquez la classe avec l’attribut `SerializePropertyNamesAsCamelCase`. Si vous souhaitez mapper les noms de propriété sur un nom différent, vous pouvez utiliser l’attribut `JsonProperty` comme la propriété `DescriptionFr` ci-dessus. L’attribut `JsonProperty` est prioritaire sur l’attribut `SerializePropertyNamesAsCamelCase`.
> 
> 

À présent que nous avons défini une classe de modèle, nous pouvons créer une définition d’index très facilement :

```csharp
var definition = new Index()
{
    Name = "hotels",
    Fields = FieldBuilder.BuildForType<Hotel>()
};
```

## <a name="create-the-index"></a>Création de l'index
Maintenant qu’un objet `Index` a été initialisé, vous pouvez créer l’index en appelant `Indexes.Create` sur votre objet `SearchServiceClient` :

```csharp
serviceClient.Indexes.Create(definition);
```

Pour une requête réussie, la méthode effectuera un retour normalement. En cas de problème avec la requête, comme un paramètre non valide, la méthode lève `CloudException`.

Pour supprimer un index, il vous suffit d’appeler la méthode `Indexes.Delete` sur votre `SearchServiceClient`. Voici, par exemple, la requête à utiliser pour supprimer l’index « hotels » :

```csharp
serviceClient.Indexes.Delete("hotels");
```

> [!NOTE]
> L'exemple de code dans cet article utilise les méthodes synchrones du SDK .NET Azure Search pour plus de simplicité. Nous vous recommandons d'utiliser les méthodes asynchrones dans vos propres applications pour les rendre évolutives et réactives. Par exemple, dans les exemples ci-dessus, vous pouvez utiliser `CreateAsync` et `DeleteAsync` au lieu de `Create` et `Delete`.
> 
> 

## <a name="next-steps"></a>Étapes suivantes
Après avoir créé un index Azure Search, vous pouvez commencer à [télécharger du contenu dans votre index](search-what-is-data-import.md) afin d’y lancer des recherches.

