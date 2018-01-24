---
title: Filtres de facette dans la Recherche Azure | Microsoft Docs
description: "Effectuez un filtrage selon des critères par identité de sécurité d’utilisateur, langue, emplacement géographique ou valeurs numériques pour réduire les résultats de recherche des requêtes effectuées dans Recherche Azure, service de recherche dans le cloud hébergé sur Microsoft Azure."
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
ms.assetid: 
ms.service: search
ms.devlang: 
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 10/13/2017
ms.author: heidist
ms.openlocfilehash: 02a027845e56407bc8cc95f54a46d9534cb6de92
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/18/2017
---
# <a name="how-to-build-a-facet-filter-in-azure-search"></a>Comment créer un filtre de facette dans la Recherche Azure 

La navigation par facettes est utilisée pour le filtrage autonome sur les résultats d’une requête dans une application de recherche, dans le cadre de laquelle votre application propose des contrôles d’interface utilisateur permettant de limiter une recherche à des groupes de documents (par exemple, des catégories de marques) ; la Recherche Azure fournit la structure de données sur laquelle reposera l’opération. Dans cet article, nous passerons rapidement en revue les étapes de base de la procédure de création d’une structure de navigation par facettes, sur laquelle sera basée l’expérience de recherche que vous souhaitez proposer. 

> [!div class="checklist"]
> * Choisissez des champs pour le filtrage et la création de facettes
> * Définissez des attributs sur le champ
> * Créez l’index et chargez les données
> * Ajoutez des filtres de facettes à une requête
> * Gérez les résultats

Les facettes sont dynamiques et renvoyées sur une requête. Les réponses associées à une recherche incluent les catégories de facettes utilisées pour parcourir les résultats. Si vous n’êtes pas familiarisé avec les facettes, l’exemple suivant illustre une structure de navigation par facettes.

  ![](./media/search-filters-facets/facet-nav.png)

Vous découvrez ce type de navigation et souhaitez en savoir plus ? Consultez la section [Implémentation de la navigation à facettes dans la Recherche Azure](search-faceted-navigation.md).

## <a name="choose-fields"></a>Choisir des champs

Vous pouvez calculer des facettes sur la base de champs à une seule valeur, ou de collections. Les champs les plus efficaces dans une navigation par facettes présentent une cardinalité faible (un petit nombre de valeurs distinctes, qui se répètent tout au long d’un document dans votre corpus de recherche, par exemple une liste de couleurs, de pays ou de noms de marques). 

La création de facettes est activée champ par champ lorsque vous générez l’index, si vous définissez les attributs `filterable` et `facetable` sur TRUE. Seuls les champs filtrables peuvent être désignés comme étant à facettes.

Tous les [types de champs](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) pouvant être utilisés dans la navigation par facettes sont indiqués comme ayant la valeur « facetable » :

+ Edm.String
+ Edm.DateTimeOffset
+ Edm.Boolean
+ Edm.Collections
+ Types de champs numériques : Edm.Int32, Edm.Int64, Edm.Double

Vous ne pouvez pas utiliser le type Edm.GeographyPoint dans une navigation par facettes. Les facettes sont construites à partir de texte ou de chiffres lisibles pour l’être humain. Par conséquent, elles ne sont pas prises en charge pour les coordonnées géographiques. Vous aurez besoin d’un champ de ville ou de région pour les facettes créées par lieu.

## <a name="set-attributes"></a>Définir des attributs

Les attributs d’index qui contrôlent l’utilisation d’un champ sont ajoutés aux définitions de champs individuels dans l’index. Dans l’exemple suivant, les champs présentant une faible cardinalité, utiles pour les facettes, incluent une catégorie (hôtel, motel, auberge de jeunesse...), des équipements et des évaluations. 

Dans l’API .NET, les attributs de filtrage doivent être définis explicitement. Dans l’API REST, les facettes et le filtrage sont activés par défaut, ce qui signifie que vous devez définir explicitement les attributs uniquement lorsque vous souhaitez les désactiver. Bien que cela ne soit pas nécessaire d’un point de vue technique, nous affichons les attributions dans l’exemple REST suivant, à des fins d’enseignement. 

> [!Tip]
> Pour optimiser le stockage et les performances, une meilleure pratique consiste à désactiver la création de facettes pour les champs qui ne doivent pas être utilisés en tant que facettes. En particulier, les champs de chaîne pour les valeurs singleton, comme un ID ou un nom de produit, doivent être définis sur « Facetable » pour empêcher leur utilisation accidentelle (et inefficace) dans la navigation par facettes.


```http
{
    "name": "hotels",  
    "fields": [
        {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false},
        {"name": "baseRate", "type": "Edm.Double"},
        {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
        {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
        {"name": "hotelName", "type": "Edm.String", "facetable": false},
        {"name": "category", "type": "Edm.String", "filterable": true, "facetable": true},
        {"name": "tags", "type": "Collection(Edm.String)", "filterable": true, "facetable": true},
        {"name": "parkingIncluded", "type": "Edm.Boolean",  "filterable": true, "facetable": true, "sortable": false},
        {"name": "smokingAllowed", "type": "Edm.Boolean", "filterable": true, "facetable": true, "sortable": false},
        {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
        {"name": "rating", "type": "Edm.Int32", "filterable": true, "facetable": true},
        {"name": "location", "type": "Edm.GeographyPoint"}
    ]
}
```

> [!Note]
> Cette définition d’index est copiée à partir de la section relative à la [création d’un index de Recherche Azure à l’aide de l’API REST](https://docs.microsoft.com/azure/search/search-create-index-rest-api). Il est identique, à l’exception de légères différences dans les définitions de champ. Les attributs « filterable » et « facetable » sont ajoutés de manière explicite dans les champs « category », « tags », « parkingIncluded », « smokingAllowed » et « rating ». Dans la pratique, vous obtenez gratuitement les attributs « filterable » et « facetable » sur les types de champ Edm.String, Edm.Boolean et Edm.Int32. 

## <a name="build-and-load-an-index"></a>Créer et charger un index

La [création et le remplissage de l’index](https://docs.microsoft.com/azure/search/search-create-index-dotnet#create-the-index) sont une étape intermédiaire (et peut-être évidente) avant la formulation d’une requête. Nous signalons cette étape ici par souci d’exhaustivité. Une manière de déterminer si l’index est disponible consiste à vérifier la liste des index dans le [portail](https://portal.azure.com).

## <a name="add-facet-filters-to-a-query"></a>Ajoutez des filtres de facettes à une requête

Dans le code d’application, construisez une requête spécifiant toutes les parties d’une requête valide, y compris les expressions de recherche, les facettes, les filtres, les profils de score, à savoir tout ce qui sert à formuler une requête. L’exemple suivant génère une requête qui crée une navigation par facettes selon le type d’hébergement, d’évaluation et d’autres équipements.

```csharp
SearchParameters sp = new SearchParameters()
{
  ...
  // Add facets
  Facets = new List<String>() { "category", "rating", "parkingIncluded", "smokingAllowed" },
};
```

### <a name="return-filtered-results-on-click-events"></a>Renvoyer des résultats filtrés sur les événements clic

L’expression de filtre gère l’événement clic sur la valeur de facette. Si vous disposez d’une facette « Category », un clic sur la catégorie « motel » est implémenté via une expression `$filter` qui sélectionne les hébergements de ce type. Lorsqu’un utilisateur clique sur « motels » pour indiquer que seul ce type d’hébergement doit être affiché, la requête suivante que l’application envoie inclut la chaîne : $filter=category eq ‘motels’.

L’extrait de code suivant ajoute la catégorie au filtre si l’utilisateur sélectionne une valeur à partir de la facette « Category ».

```csharp
if (categoryFacet != "")
  filter = "category eq '" + categoryFacet + "'";
```
À l’aide de l’API REST, la requête est articulée sous une forme similaire à `$filter=category eq 'c1'`. Pour faire d’une catégorie un champ à valeurs multiples, utilisez la syntaxe suivante : `$filter=category/any(c: c eq 'c1')`

## <a name="tips-and-workarounds"></a>Conseils et solutions de contournement

### <a name="initialize-a-page-with-facets-in-place"></a>Initialiser une page sur laquelle des facettes sont définies

Si vous souhaitez initialiser une page sur laquelle des facettes sont définies, vous pouvez envoyer une requête dans le cadre de l’initialisation de la page, afin d’amorcer la page avec une structure de facette initiale.

### <a name="preserve-a-facet-navigation-structure-asynchronously-of-filtered-results"></a>Conserver une structure de navigation par facettes de manière asynchrone par rapport aux résultats filtrés

L’une des difficultés liées à la navigation par facettes dans la Recherche Azure est le fait que les facettes existent uniquement pour les résultats actuels. Dans la pratique, il est courant de conserver un ensemble statique de facettes, afin que l’utilisateur puisse naviguer dans l’ordre inverse, en suivant les étapes déjà parcourues pour explorer les autres chemins d’accès via le contenu de la recherche. 

Il s’agit d’un cas d’usage courant, mais non fourni de manière prête à l’emploi par la structure de navigation par facettes. Les développeurs qui veulent utiliser des facettes statiques contournent cette limitation en émettant deux requêtes filtrées, l’une dont la portée se limite aux résultats et l’autre, qui permet de créer une liste statique de facettes pour la navigation.

## <a name="see-also"></a>Voir aussi

+ [Filtres dans Recherche Azure](search-filters.md)
+ [Création d’une API REST d’index](https://docs.microsoft.com/rest/api/searchservice/create-index)
+ [API REST de recherche de documents](https://docs.microsoft.com/rest/api/searchservice/search-documents)

