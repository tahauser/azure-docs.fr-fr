---
title: "Créer un index (API REST - Recherche Azure) | Microsoft Docs"
description: "Créer un index dans le code à l’aide de l’API REST HTTP d’Azure Search."
services: search
documentationcenter: 
author: ashmaka
manager: jhubbard
editor: 
tags: azure-portal
ms.assetid: ac6c5fba-ad59-492d-b715-d25a7a7ae051
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 12/08/2016
ms.author: ashmaka
ms.openlocfilehash: 9a64d1436471e406b7d9b700257d3dd96b5edcde
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/18/2017
---
# <a name="create-an-azure-search-index-using-the-rest-api"></a>Création d’un index Azure Search à l’aide de l’API REST
> [!div class="op_single_selector"]
>
> * [Vue d'ensemble](search-what-is-an-index.md)
> * [Portail](search-create-index-portal.md)
> * [.NET](search-create-index-dotnet.md)
> * [REST](search-create-index-rest-api.md)
>
>

Cet article vous guidera dans la création d’un [index](https://docs.microsoft.com/rest/api/searchservice/Create-Index) Azure Search à l’aide de l’API REST Azure Search.

Avant de suivre ce guide et de passer à la création d’un index, vous devez avoir déjà [créé un service Azure Search](search-create-service-portal.md).

Pour créer un index Azure Search à l’aide de l’API REST, vous allez générer une seule requête POST HTTP sur le point de terminaison URL de votre service Azure Search. Votre définition d’index figurera dans le corps de la requête sous la forme d’un contenu JSON correct.

## <a name="identify-your-azure-search-services-admin-api-key"></a>Identifier la clé API d’administration de votre service Azure Search
Maintenant que vous avez configuré un service Azure Search, vous pouvez émettre des requêtes HTTP sur le point de terminaison URL de votre service à l’aide de l’API REST. *Toutes* les requêtes d’API doivent inclure la clé API générée pour le service Recherche que vous avez approvisionné. L’utilisation d’une clé valide permet d’établir, en fonction de chaque demande, une relation de confiance entre l’application qui envoie la demande et le service qui en assure le traitement.

1. Pour accéder aux clés API de votre service, vous devez vous connecter au [Portail Azure](https://portal.azure.com/)
2. Accédez au panneau de votre service Azure Search
3. Cliquez sur l’icône « Clés »

Votre service comporte à la fois des *clés d’administration* et des *clés de requête*.

* Les *clés d’administration* principales et secondaires vous accordent des droits d’accès complets à toutes les opérations, avec notamment la possibilité de gérer le service ou de créer et supprimer des index, des indexeurs et des sources de données. Deux clés sont à votre disposition afin que vous puissiez continuer à utiliser la clé secondaire si vous décidez de régénérer la clé primaire et inversement.
* Vos *clés de requête* vous accordent un accès en lecture seule aux index et documents ; elles sont généralement distribuées aux applications clientes qui émettent des demandes de recherche.

Dans le cadre de la création d’un index, vous pouvez utiliser votre clé d’administration principale ou secondaire.

## <a name="define-your-azure-search-index-using-well-formed-json"></a>Définir votre index Azure Search à l’aide d’un code JSON correct
Une simple requête HTTP POST transmise à votre service vous permet de créer votre index. Le corps de votre requête HTTP POST doit contenir un seul objet JSON qui définit votre index Azure Search.

1. La première propriété de cet objet JSON correspond au nom de l’index.
2. La deuxième propriété de cet objet JSON est un tableau JSON nommé `fields` , qui contient un objet JSON distinct pour chaque champ de votre index. Chacun de ces objets JSON contient plusieurs paires nom/valeur pour chacun des attributs de champ, à savoir « nom », « type », etc.

Il est important de ne perdre de vue ni votre expérience de recherche ni vos besoins métiers lorsque vous concevez votre index, chaque champ devant être associé à des [attributs corrects](https://docs.microsoft.com/rest/api/searchservice/Create-Index). Ces attributs déterminent les fonctionnalités de recherche (filtrage, facettes, tri de recherche en texte intégral, etc.) qui s’appliqueront à chaque champ. Si vous ne spécifiez pas d’attribut, le paramètre par défaut consistera à activer la fonctionnalité de recherche correspondante à moins que vous ne la désactiviez spécifiquement.

Dans notre exemple, nous avons nommé notre index « hotels » et défini ses champs de la manière suivante :

```JSON
{
    "name": "hotels",  
    "fields": [
        {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false},
        {"name": "baseRate", "type": "Edm.Double"},
        {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
        {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
        {"name": "hotelName", "type": "Edm.String", "facetable": false},
        {"name": "category", "type": "Edm.String"},
        {"name": "tags", "type": "Collection(Edm.String)"},
        {"name": "parkingIncluded", "type": "Edm.Boolean", "sortable": false},
        {"name": "smokingAllowed", "type": "Edm.Boolean", "sortable": false},
        {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
        {"name": "rating", "type": "Edm.Int32"},
        {"name": "location", "type": "Edm.GeographyPoint"}
    ]
}
```

Nous avons soigneusement choisi les attributs d’index pour chaque champ en fonction de la manière dont ils seront vraisemblablement utilisés dans une application. Par exemple, `hotelId` est une clé unique qui sera vraisemblablement méconnue des utilisateurs effectuant une recherche d’hôtels. Nous allons donc désactiver la recherche en texte intégral pour ce champ en définissant le paramètre `searchable` sur `false`, afin d’économiser de l’espace au niveau de l’index.

Notez que, dans votre index, un seul champ de type `Edm.String` doit être désigné en tant que « clé ».

La définition d’index ci-dessus utilise un analyseur de langue pour le champ `description_fr` dans la mesure où il est destiné à stocker du texte en français. Pour plus d’informations sur les analyseurs de langue, consultez [l’article relatif à la prise en charge linguistique](https://docs.microsoft.com/rest/api/searchservice/Language-support), ainsi que le [billet de blog](https://azure.microsoft.com/blog/language-support-in-azure-search/) correspondant.

## <a name="issue-the-http-request"></a>Envoyer la requête HTTP
1. En utilisant votre définition d’index dans le corps de votre requête, envoyez une requête HTTP POST vers l’URL de point de terminaison de votre service Azure Search. Dans l’URL, veillez à utiliser le nom de votre service en tant que nom d’hôte et placez l’attribut `api-version` approprié comme paramètre de chaîne de requête (à la date de publication de ce document, l’API `2016-09-01` correspond à la version la plus récente).
2. Dans les en-têtes de requête, spécifiez `Content-Type` comme `application/json`. Vous devrez également renseigner dans l’en-tête `api-key` la clé d’administration de votre service que vous avez identifiée à l’étape I.

Vous devrez fournir vos propres nom de service et clé d’API pour émettre la requête ci-dessous :

    POST https://[service name].search.windows.net/indexes?api-version=2016-09-01
    Content-Type: application/json
    api-key: [api-key]


Pour que votre requête aboutisse, vous devez voir le code d’état « 201 créé ». Pour plus d’informations sur la création d’un index par le biais de l’API REST, consultez la [référence API ici](https://docs.microsoft.com/rest/api/searchservice/Create-Index). Pour plus d’informations sur les autres codes d’état HTTP pouvant être renvoyés en cas d’échec, consultez la page [Codes d’état HTTP (Azure Search)](https://docs.microsoft.com/rest/api/searchservice/HTTP-status-codes).

Pour supprimer un index, il vous suffit de générer une requête HTTP DELETE. Voici, par exemple, la requête à utiliser pour supprimer l’index « hotels » :

    DELETE https://[service name].search.windows.net/indexes/hotels?api-version=2016-09-01
    api-key: [api-key]


## <a name="next-steps"></a>Étapes suivantes
Après avoir créé un index Azure Search, vous pouvez commencer à [télécharger du contenu dans votre index](search-what-is-data-import.md) afin d’y lancer des recherches.
