---
title: "Requêtes dans Recherche Azure | Documents Microsoft"
description: "Créez une requête de recherche dans Azure Search et utilisez des paramètres de recherche pour filtrer et trier les résultats de recherche."
services: search
manager: jhubbard
documentationcenter: 
author: ashmaka
ms.assetid: 69205d7a-363f-4b92-a53f-6ca818a3d2c7
ms.service: search
ms.devlang: na
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 11/13/2017
ms.author: ashmaka
ms.openlocfilehash: b1a96c2299e415db89f881fa72b150d50bb3c69c
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/18/2017
---
# <a name="queries-in-azure-search"></a>Requêtes dans Recherche Azure
> [!div class="op_single_selector"]
> * [Vue d'ensemble](search-query-overview.md)
> * [Portail](search-explorer.md)
> * [.NET](search-query-dotnet.md)
> * [REST](search-query-rest-api.md)
> 
> 

Lorsque vous soumettez des demandes de recherche à Azure Search, vous pouvez spécifier plusieurs paramètres avec les mots que vous tapez dans la zone de recherche de votre application. Ces paramètres de requête vous permettent d’obtenir un contrôle plus étroit de [l’expérience de recherche en texte intégral](search-lucene-query-architecture.md).

Vous trouverez ci-dessous une liste qui explique brièvement les utilisations courantes des paramètres de requête dans Azure Search. Pour une étude complète des paramètres de requête et de leur comportement, consultez les pages détaillées de [l’API REST](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) et du [SDK .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters#microsoft_azure_search_models_searchparameters#properties_summary).

## <a name="types-of-queries"></a>Types de requête
Azure Search propose de nombreuses options pour créer des requêtes extrêmement performantes. Les deux types principaux de requête que vous allez utiliser sont `search` et `filter`. Une requête `search` recherche un ou plusieurs termes dans tous les champs *utilisables dans une recherche* de votre index, et fonctionne à l’instar des moteurs de recherche Google ou Bing. Une requête `filter` permet d’évaluer une expression booléenne dans tous les champs *filtrables* d’un index. Contrairement aux requêtes `search`, les requêtes `filter` correspondent au contenu exact d’un champ, ce qui signifie qu’elles respectent la casse pour les champs de chaîne.

Vous pouvez utiliser des recherches et des filtres conjointement ou séparément. Si vous les utilisez conjointement, le filtre est d’abord appliqué à la totalité de l’index et la recherche est effectuée sur les résultats du filtre. Les filtres peuvent donc être utiles pour améliorer les performances des requêtes, puisqu’ils limitent le nombre de documents que devra traiter la requête de recherche.

La syntaxe des expressions de filtre est un sous-ensemble du [langage de filtre OData](https://docs.microsoft.com/rest/api/searchservice/OData-Expression-Syntax-for-Azure-Search). Pour les requêtes de recherche, vous pouvez utiliser la [syntaxe simplifiée](https://docs.microsoft.com/rest/api/searchservice/Simple-query-syntax-in-Azure-Search) ou la [syntaxe de requête Lucene](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search) qui sont présentées ci-dessous.

### <a name="simple-query-syntax"></a>Syntaxe de requête simple
Le [syntaxe de requête simple](https://docs.microsoft.com/rest/api/searchservice/Simple-query-syntax-in-Azure-Search) est le langage de requête par défaut utilisé dans Azure Search. Elle prend en charge un certain nombre d’opérateurs de recherche courants, notamment les opérateurs AND, OR, NOT, les expressions, les suffixes et les opérateurs de priorité.

### <a name="lucene-query-syntax"></a>syntaxe de requête Lucene
La [syntaxe de requête Lucene](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search) vous permet d’utiliser le langage de requête expressif et largement adopté développé dans le cadre [d’Apache Lucene](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html).

L’utilisation de cette syntaxe de requête vous permet d’obtenir facilement les fonctionnalités suivantes : [requêtes portant sur un champ](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_fields), [recherche partielle](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_fuzzy), [recherche de proximité](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_proximity), [augmentation de la pertinence de termes](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_termboost), [recherche d’expression régulière](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_regex), [recherche par caractères génériques](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_wildcard), [notions fondamentales de syntaxe](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_syntax) et [requêtes utilisant des opérateurs booléens](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_boolean).

## <a name="ordering-results"></a>Classement des résultats
Lors de la réception des résultats d’une requête de recherche, vous pouvez demander qu’Azure Search produise les résultats classés par valeurs dans un champ spécifique. Par défaut, Azure Search classe les résultats en fonction du rang du résultat de la recherche de chaque document, qui est dérivé de la méthode [TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf).

Si vous souhaitez qu’Azure Search retourne les résultats en les classant avec une valeur autre que le résultat de la recherche, vous pouvez utiliser le paramètre de recherche `orderby` . Vous pouvez spécifier la valeur du paramètre `orderby` pour inclure les noms de champ et les appels à la [fonction `geo.distance()`](https://docs.microsoft.com/rest/api/searchservice/OData-Expression-Syntax-for-Azure-Search) pour les valeurs géospatiales. Chaque expression peut être suivie par `asc` pour indiquer que les résultats sont demandés dans l’ordre croissant, et par `desc` pour indiquer que les résultats sont demandés dans l’ordre décroissant. Le classement par défaut est l’ordre croissant.

## <a name="paging"></a>Pagination
Azure Search facilite l’implémentation de la pagination des résultats de recherche. À l’aide des paramètres `top` et `skip`, vous pouvez facilement émettre des demandes de recherche qui vous permettent de recevoir l’ensemble total des résultats de recherche dans des sous-ensembles gérables, ordonnés qui permettent de bonnes pratiques de recherche dans l’interface utilisateur. Lors de la réception de ces sous-ensembles de résultats plus petits, vous pouvez également recevoir le nombre de documents dans l’ensemble total des résultats de la recherche.

Pour plus d’informations sur la pagination des résultats de recherche, consultez l’article [Navigation dans les résultats de recherche d’Azure Search](search-pagination-page-layout.md).

## <a name="hit-highlighting"></a>Mise en surbrillance des correspondances
Dans Azure Search, vous pouvez mettre facilement en évidence la partie exacte des résultats de recherche qui correspondent à la requête de recherche en utilisant les paramètres `highlight`, `highlightPreTag` et `highlightPostTag`. Vous pouvez spécifier les champs *utilisables dans une recherche* dont le texte correspondant à la requête doit être mis en évidence ainsi que les balises de chaîne exactes à ajouter au début et à la fin du texte correspondant retourné par le service Recherche Azure.

## <a name="try-out-query-syntax"></a>Essais de syntaxe de requête

La meilleure façon de comprendre les différences de syntaxe est de soumettre des requêtes et d’examiner les résultats.

+ Utilisez l’[Explorateur de recherche](search-explorer.md) dans le portail Azure. En déployant l’[index d’exemple](search-get-started-portal.md), vous pouvez interroger l’index en quelques minutes à l’aide des outils du portail.

+ Utilisez Telerik Fiddler ou Postman Chrome pour soumettre des requêtes à un index que vous avez chargé sur votre service de recherche. Les deux outils prennent en charge les appels REST vers un point de terminaison HTTP. 