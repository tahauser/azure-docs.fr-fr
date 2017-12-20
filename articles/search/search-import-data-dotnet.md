---
title: "Charger des données (.NET - Recherche Azure) | Microsoft Docs"
description: "Découvrez comment charger des données dans un index dans Azure Search à l’aide du Kit de développement logiciel (SDK) .NET."
services: search
documentationcenter: 
author: brjohnstmsft
manager: jhubbard
editor: 
tags: 
ms.assetid: 0e0e7e7b-7178-4c26-95c6-2fd1e8015aca
ms.service: search
ms.devlang: dotnet
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 01/13/2017
ms.author: brjohnst
ms.openlocfilehash: bdd952869143c6ca6374bb9264db5bcba1f32b50
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/18/2017
---
# <a name="upload-data-to-azure-search-using-the-net-sdk"></a>Charger des données dans Azure Search à l’aide du Kit de développement logiciel (SDK) .NET
> [!div class="op_single_selector"]
> * [Vue d'ensemble](search-what-is-data-import.md)
> * [.NET](search-import-data-dotnet.md)
> * [REST](search-import-data-rest-api.md)
> 
> 

Cet article vous explique comment utiliser le [Kit de développement logiciel (SDK) .NET Azure Search](https://aka.ms/search-sdk) pour importer des données dans un index Azure Search.

Avant de commencer cette procédure, vous devez déjà avoir [créé un index Azure Search](search-what-is-an-index.md). Cet article suppose également que vous avez déjà créé un objet `SearchServiceClient` , comme indiqué dans [Créer un index Azure Search à l’aide du Kit de développement logiciel (SDK) .NET](search-create-index-dotnet.md#CreateSearchServiceClient).

> [!NOTE]
> Tous les exemples de code figurant dans cet article sont écrits en C#. L’intégralité du code source est disponible [sur GitHub](http://aka.ms/search-dotnet-howto). Vous pouvez également consulter le [kit de développement logiciel (SDK) .NET Azure Search](search-howto-dotnet-sdk.md) pour une description plus détaillée de l’exemple de code.

Afin de transmettre des documents à l’index à l’aide du Kit de développement logiciel (SDK) .NET, vous devez :

1. Créer un objet `SearchIndexClient` à connecter à votre index de recherche.
2. Créer un `IndexBatch` contenant les documents à ajouter, à modifier ou à supprimer.
3. Appeler la méthode `Documents.Index` de votre `SearchIndexClient` pour envoyer `IndexBatch` à votre index de recherche.

## <a name="create-an-instance-of-the-searchindexclient-class"></a>Créer une instance de la classe SearchIndexClient
Pour importer des données dans l’index à l’aide du Kit de développement logiciel (SDK) .NET Azure Search, vous devez créer une instance de la classe `SearchIndexClient` . Vous pouvez construire cette instance vous-même, mais la procédure est plus facile si vous avez déjà une instance `SearchServiceClient` pour appeler sa méthode `Indexes.GetClient`. Par exemple, voici comment vous obtenez un `SearchIndexClient` pour l’index nommé « hotels » d’un `SearchServiceClient` nommé `serviceClient` :

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

> [!NOTE]
> Dans une application de recherche classique, le remplissage et la gestion des index sont gérés par un composant séparé des requêtes de recherche. `Indexes.GetClient` est pratique pour remplir un index car il vous évite de fournir un autre `SearchCredentials`. Pour ce faire, il transmet la clé d’administration que vous avez utilisée afin de créer le `SearchServiceClient` dans le nouveau `SearchIndexClient`. Toutefois, dans la partie de votre application qui exécute des requêtes, il vaut mieux créer le `SearchIndexClient` directement afin de transmettre une clé de requête au lieu d'une clé d'administration. Cela est conforme au [principe du moindre privilège](https://en.wikipedia.org/wiki/Principle_of_least_privilege) et vous permet de mieux sécuriser votre application. Pour plus d’informations sur les clés d’administration et les clés de requête, consultez [l’article de référence sur l’API REST du service Recherche Azure](https://docs.microsoft.com/rest/api/searchservice/).
> 
> 

`SearchIndexClient` a une propriété `Documents`. Cette propriété fournit toutes les méthodes dont vous avez besoin pour ajouter, modifier, supprimer ou interroger des documents dans l’index.

## <a name="decide-which-indexing-action-to-use"></a>Déterminer l’action d’indexation à utiliser
Pour importer des données à l’aide du Kit de développement logiciel (SDK) .NET, vous devez empaqueter vos données dans un objet `IndexBatch` . Un `IndexBatch` encapsule une collection d’objets `IndexAction`, chacun d’entre eux contenant un document et une propriété qui indique à Azure Search les actions à effectuer sur ce document (téléchargement, fusion, suppression, etc.). Selon le type d’action que vous allez choisir, seuls certains champs doivent être inclus dans chaque document :

| Action | Description | Champs requis pour chaque document | Remarques |
| --- | --- | --- | --- |
| `Upload` |Une action `Upload` est similaire à celle d’un « upsert », où le document est inséré s’il est nouveau et mis à jour/remplacé s’il existe déjà. |une clé, ainsi que tout autre champ que vous souhaitez définir |Lors de la mise à jour ou du remplacement d’un document existant, un champ qui n’est pas spécifié dans la requête sera défini sur la valeur `null`, y compris lorsque le champ a été précédemment défini sur une valeur non null. |
| `Merge` |Met à jour un document existant avec les champs spécifiés. Si le document n’existe pas dans l’index, la fusion échoue. |une clé, ainsi que tout autre champ que vous souhaitez définir |N'importe quel champ que vous spécifiez dans une fusion remplace le champ existant dans le document. Cela inclut les champs de type `DataType.Collection(DataType.String)`. Par exemple, si le document contient un champ `tags` avec la valeur `["budget"]` et que vous exécutez une fusion avec la valeur `["economy", "pool"]` pour le champ `tags`, la valeur finale du champ `tags` sera `["economy", "pool"]`, et non `["budget", "economy", "pool"]`. |
| `MergeOrUpload` |Cette action est similaire à celle d’une action `Merge` s’il existe déjà dans l’index un document comportant la clé spécifiée. Dans le cas contraire, elle exécutera une action `Upload` avec un nouveau document. |une clé, ainsi que tout autre champ que vous souhaitez définir |- |
| `Delete` |Cette action supprime de l’index le document spécifié. |clé uniquement |Tous les champs que vous spécifiez en dehors du champ de clé sont ignorés. Si vous souhaitez supprimer un champ individuel dans un document, utilisez plutôt `Merge` et définissez simplement le champ de manière explicite sur la valeur null. |

Vous pouvez spécifier quelle action vous souhaitez utiliser avec les différentes méthodes statiques des classes `IndexBatch` et `IndexAction`, comme indiqué dans la section suivante.

## <a name="construct-your-indexbatch"></a>Construire votre IndexBatch
Maintenant que vous connaissez les actions à effectuer sur vos documents, vous êtes prêt à construire `IndexBatch`. L’exemple ci-dessous montre comment créer un lot avec différentes actions. Notez que notre exemple utilise une classe personnalisée appelée `Hotel` qui est mappée à un document dans l’index « hotels ».

```csharp
var actions =
    new IndexAction<Hotel>[]
    {
        IndexAction.Upload(
            new Hotel()
            {
                HotelId = "1",
                BaseRate = 199.0,
                Description = "Best hotel in town",
                DescriptionFr = "Meilleur hôtel en ville",
                HotelName = "Fancy Stay",
                Category = "Luxury",
                Tags = new[] { "pool", "view", "wifi", "concierge" },
                ParkingIncluded = false,
                SmokingAllowed = false,
                LastRenovationDate = new DateTimeOffset(2010, 6, 27, 0, 0, 0, TimeSpan.Zero),
                Rating = 5,
                Location = GeographyPoint.Create(47.678581, -122.131577)
            }),
        IndexAction.Upload(
            new Hotel()
            {
                HotelId = "2",
                BaseRate = 79.99,
                Description = "Cheapest hotel in town",
                DescriptionFr = "Hôtel le moins cher en ville",
                HotelName = "Roach Motel",
                Category = "Budget",
                Tags = new[] { "motel", "budget" },
                ParkingIncluded = true,
                SmokingAllowed = true,
                LastRenovationDate = new DateTimeOffset(1982, 4, 28, 0, 0, 0, TimeSpan.Zero),
                Rating = 1,
                Location = GeographyPoint.Create(49.678581, -122.131577)
            }),
        IndexAction.MergeOrUpload(
            new Hotel()
            {
                HotelId = "3",
                BaseRate = 129.99,
                Description = "Close to town hall and the river"
            }),
        IndexAction.Delete(new Hotel() { HotelId = "6" })
    };

var batch = IndexBatch.New(actions);
```

Dans ce cas, nous utilisons `Upload`, `MergeOrUpload` et `Delete` comme actions de recherche, tel que spécifié par les méthodes appelées sur la classe `IndexAction`.

Supposons que cet exemple d’index « hotels » contient déjà un certain nombre de documents. Notez que nous n’avons pas eu à spécifier tous les champs de document possibles en utilisant `MergeOrUpload` et que nous n’avons fait que spécifier la clé de document (`HotelId`) avec la commande `Delete`.

Notez également que chaque requête d’indexation ne peut contenir que 1 000 documents maximum.

> [!NOTE]
> Dans cet exemple, nous appliquons différentes actions à différents documents. Si vous souhaitez effectuer ces mêmes actions sur tous les documents du lot, au lieu d’appeler `IndexBatch.New`, vous pouvez utiliser les autres méthodes statiques de `IndexBatch`. Par exemple, vous pouvez créer des lots en appelant `IndexBatch.Merge`, `IndexBatch.MergeOrUpload` ou `IndexBatch.Delete`. Ces méthodes prennent une collection de documents (objets de type `Hotel` dans cet exemple) à la place d’objets `IndexAction`.
> 
> 

## <a name="import-data-to-the-index"></a>Importer les données dans l’index
Maintenant qu’un objet `IndexBatch` a été initialisé, vous pouvez l’envoyer à l’index en appelant `Documents.Index` sur votre objet `SearchIndexClient`. L’exemple suivant illustre l’appel à `Index`, ainsi que des étapes supplémentaires que vous devez effectuer :

```csharp
try
{
    indexClient.Documents.Index(batch);
}
catch (IndexBatchException e)
{
    // Sometimes when your Search service is under load, indexing will fail for some of the documents in
    // the batch. Depending on your application, you can take compensating actions like delaying and
    // retrying. For this simple demo, we just log the failed document keys and continue.
    Console.WriteLine(
        "Failed to index some of the documents: {0}",
        String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
}

Console.WriteLine("Waiting for documents to be indexed...\n");
Thread.Sleep(2000);
```

Notez le signe `try`/`catch` entourant l’appel à la méthode `Index`. Le bloc catch gère un cas d’erreur important pour l’indexation. Si votre service Azure Search ne parvient pas à indexer certains documents du lot, `Documents.Index` génère un `IndexBatchException`. Cela peut se produire si vous indexez des documents lorsque votre service est surchargé. **Nous vous recommandons vivement de prendre en charge explicitement ce cas de figure dans votre code.** Vous pouvez retarder puis relancer l'indexation des documents qui ont échoué, ouvrir une session et continuer comme dans l’exemple, ou faire autre chose selon la cohérence des données requise par votre application.

Enfin, le code de l’exemple ci-dessus attend deux secondes. L'indexation s’exécutant en mode asynchrone dans votre service Azure Search, l'exemple d'application doit attendre quelque temps afin de s'assurer que les documents sont disponibles pour la recherche. Ce genre de retard n’est nécessaire que dans les démonstrations, les tests et les exemples d'applications.

<a name="HotelClass"></a>

### <a name="how-the-net-sdk-handles-documents"></a>Gestion des documents par le Kit de développement logiciel (SDK) .NET
Vous demandez peut-être comment le SDK .NET Azure Search peut charger des instances d’une classe définie par l'utilisateur, comme `Hotel` , dans l'index. Pour répondre à cette question, examinons la classe `Hotel` qui mappe le schéma d’index défini dans [Créer un index Azure Search à l’aide du Kit de développement logiciel (SDK) .NET](search-create-index-dotnet.md#DefineIndex):

```csharp
[SerializePropertyNamesAsCamelCase]
public partial class Hotel
{
    [Key]
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

    // ToString() method omitted for brevity...
}
```

La première chose à remarquer est que chaque propriété publique de `Hotel` correspond à un champ dans la définition de l'index, mais à une différence près : le nom de chaque champ commence par une minuscule, tandis que le nom de chaque propriété publique de `Hotel` commence par une majuscule. Il s'agit d'un scénario courant dans les applications .NET qui effectuent une liaison de données là où le schéma cible est en dehors du contrôle du développeur de l'application. Plutôt que de violer les consignes d’affectation de noms de .NET en faisant commencer les noms de propriété par une minuscule, vous pouvez demander au SDK d’attribuer automatiquement une casse minuscule aux noms de propriété avec l’attribut `[SerializePropertyNamesAsCamelCase]` .

> [!NOTE]
> Le SDK .NET Azure Search utilise la bibliothèque [NewtonSoft JSON.NET](http://www.newtonsoft.com/json/help/html/Introduction.htm) pour sérialiser et désérialiser vos objets de modèle personnalisés vers et à partir de JSON. Vous pouvez personnaliser cette sérialisation si nécessaire. Pour plus d’informations, consultez l’article [Sérialisation personnalisée avec JSON.NET](search-howto-dotnet-sdk.md#JsonDotNet). En voici un exemple : l’utilisation de l’attribut `[JsonProperty]` sur la propriété `DescriptionFr` dans l’exemple de code ci-dessus.
> 
> 

La deuxième chose importante sur la classe `Hotel` concerne les types de données des propriétés publiques. Les types .NET de ces propriétés correspondent à leurs types de champ équivalents dans la définition de l'index. Par exemple, la propriété de chaîne `Category` correspond au champ `category`, qui est de type `DataType.String`. Il existe des mappages de type similaires entre `bool?` et `DataType.Boolean`, `DateTimeOffset?` et `DataType.DateTimeOffset`, etc. Les règles spécifiques pour le mappage de type sont documentées avec la méthode `Documents.Get` dans [l’article de référence sur le Kit de développement logiciel (SDK) .NET du service Recherche Azure](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations#Microsoft_Azure_Search_IDocumentsOperations_GetWithHttpMessagesAsync__1_System_String_System_Collections_Generic_IEnumerable_System_String__Microsoft_Azure_Search_Models_SearchRequestOptions_System_Collections_Generic_Dictionary_System_String_System_Collections_Generic_List_System_String___System_Threading_CancellationToken_).

Cette capacité à utiliser vos propres classes comme des documents fonctionne dans les deux sens. Vous pouvez également récupérer les résultats de la recherche et laisser le Kit de développement logiciel (SDK) les désérialiser automatiquement à un type de votre choix, comme indiqué dans [l’article suivant](search-query-dotnet.md).

> [!NOTE]
> Le SDK .NET Azure Search prend également en charge les documents dynamiquement typés à l’aide de la classe `Document`, qui est un mappage de type clé/valeur entre des noms de champ et des valeurs de champ. Cela est utile dans les cas où vous ne connaissez pas le schéma de l’index lors de sa conception et où il serait peu pratique d’établir une liaison à des classes de modèles spécifiques. Toutes les méthodes du SDK qui gèrent les documents ont des surcharges qui fonctionnent avec la classe `Document` , ainsi que des surcharges fortement typées qui acceptent un paramètre de type générique. Seules ces dernières sont utilisées dans l’exemple de code de cet article.
> 
> 

**Pourquoi utiliser des types de données Nullable**

Lorsque vous créez vos propres classes de modèles à mapper à un index Azure Search, nous vous recommandons de déclarer les propriétés des types de valeurs, par exemple `bool` et `int`, comme acceptant la valeur null (par exemple, `bool?` au lieu de `bool`). Si vous utilisez une propriété ne pouvant être définie sur null, vous devez **garantir** qu’aucun document de cet index ne contient de valeur null pour le champ correspondant. Ni le Kit de développement logiciel ni le service Azure Search ne vous aideront à appliquer cette recommandation.

Il ne s’agit pas d’une préoccupation hypothétique : imaginez un scénario dans lequel vous ajoutez un nouveau champ à un index existant qui est de type `DataType.Int32`. Après la mise à jour de la définition d’index, ce nouveau champ prendra la valeur null pour tous les documents (car tous les types peuvent avoir la valeur null dans Azure Search). Si vous utilisez ensuite une classe de modèle avec une propriété `int` ne pouvant être définie sur null pour ce champ, vous obtiendrez l’exception `JsonSerializationException` ci-dessous lorsque vous tenterez de récupérer des documents :

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

Pour cette raison, nous vous recommandons d'utiliser des types pour lesquels la valeur null est autorisée en tant que meilleure pratique.

## <a name="next-steps"></a>Étapes suivantes
Une fois votre index Azure Search renseigné, vous pouvez commencer à exécuter des requêtes de recherche de documents. Pour plus d’informations, consultez l’article [Interroger votre index Azure Search](search-query-overview.md) .

