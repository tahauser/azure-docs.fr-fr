---
title: "Charger des données (API REST - Recherche Azure) | Microsoft Docs"
description: "Découvrez comment charger des données sur un index dans Azure Search à l’aide de l’API REST."
services: search
documentationcenter: 
author: ashmaka
manager: jhubbard
editor: 
tags: 
ms.assetid: 8d0749fb-6e08-4a17-8cd3-1a215138abc6
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 12/08/2016
ms.author: ashmaka
ms.openlocfilehash: f22a33ed86fbfc46dfa732239263a49f34c4afee
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/18/2017
---
# <a name="upload-data-to-azure-search-using-the-rest-api"></a>Charger des données dans Azure Search à l’aide de l’API REST
> [!div class="op_single_selector"]
>
> * [Vue d'ensemble](search-what-is-data-import.md)
> * [.NET](search-import-data-dotnet.md)
> * [REST](search-import-data-rest-api.md)
>
>

Cet article vous explique comment utiliser l’ [API REST Azure Search](https://docs.microsoft.com/rest/api/searchservice/) pour importer des données dans un index Azure Search.

Avant de commencer cette procédure, vous devez déjà avoir [créé un index Azure Search](search-what-is-an-index.md).

Pour distribuer des documents dans l’index à l’aide de l’API REST, vous allez envoyer une requête HTTP POST au point de terminaison URL de votre index. Le corps de la requête HTTP est un objet JSON contenant les documents à ajouter, à modifier ou à supprimer.

## <a name="identify-your-azure-search-services-admin-api-key"></a>Identifier la clé API d’administration de votre service Azure Search
Lors de l’émission de requêtes HTTP sur votre service à l’aide de l’API REST, *chaque* demande d’API doit inclure la clé API générée pour le service Search que vous avez configuré. L’utilisation d’une clé valide permet d’établir, en fonction de chaque demande, une relation de confiance entre l’application qui envoie la demande et le service qui en assure le traitement.

1. Pour accéder aux clés API de votre service, vous pouvez vous connecter au [portail Azure](https://portal.azure.com/)
2. Accédez au panneau de votre service Azure Search
3. Cliquez sur l’icône « Clés »

Votre service comporte à la fois des *clés d’administration* et des *clés de requête*.

* Les *clés d’administration* principales et secondaires vous accordent des droits d’accès complets à toutes les opérations, avec notamment la possibilité de gérer le service ou de créer et supprimer des index, des indexeurs et des sources de données. Deux clés sont à votre disposition afin que vous puissiez continuer à utiliser la clé secondaire si vous décidez de régénérer la clé primaire et inversement.
* Vos *clés de requête* vous accordent un accès en lecture seule aux index et documents ; elles sont généralement distribuées aux applications clientes qui émettent des demandes de recherche.

Pour importer des données dans un index, vous pouvez utiliser votre clé d’administration principale ou secondaire.

## <a name="decide-which-indexing-action-to-use"></a>Déterminer l’action d’indexation à utiliser
Lorsque vous utilisez l’API REST, vous allez émettre des requêtes HTTP POST avec un corps de requête JSON à l’URL de point de terminaison de votre index Azure Search. L’objet JSON contenu dans le corps de la requête HTTP comporte un seul tableau JSON nommé « value », qui renferme les objets JSON représentant les documents que vous allez ajouter à votre index, mettre à jour ou supprimer.

Chaque objet JSON du tableau « value » représente un document à indexer. Chacun de ces objets contient les clés du document et spécifie l’action d’indexation souhaitée (téléchargement, fusion, suppression, etc.). Selon le type d’action que vous allez choisir, seuls certains champs doivent être inclus dans chaque document :

| @search.action | Description | Champs requis pour chaque document | Remarques |
| --- | --- | --- | --- |
| `upload` |Une action `upload` est similaire à celle d’un « upsert », où le document est inséré s’il est nouveau et mis à jour/remplacé s’il existe déjà. |une clé, ainsi que tout autre champ que vous souhaitez définir |Lors de la mise à jour ou du remplacement d’un document existant, un champ qui n’est pas spécifié dans la requête sera défini sur la valeur `null`, y compris lorsque le champ a été précédemment défini sur une valeur non null. |
| `merge` |Met à jour un document existant avec les champs spécifiés. Si le document n’existe pas dans l’index, la fusion échoue. |une clé, ainsi que tout autre champ que vous souhaitez définir |N'importe quel champ que vous spécifiez dans une fusion remplace le champ existant dans le document. Cela inclut les champs de type `Collection(Edm.String)`. Par exemple, si le document contient un champ `tags` avec la valeur `["budget"]` et que vous exécutez une fusion avec la valeur `["economy", "pool"]` pour le champ `tags`, la valeur finale du champ `tags` sera `["economy", "pool"]`, et non `["budget", "economy", "pool"]`. |
| `mergeOrUpload` |Cette action est similaire à celle d’une action `merge` s’il existe déjà dans l’index un document comportant la clé spécifiée. Dans le cas contraire, elle exécutera une action `upload` avec un nouveau document. |une clé, ainsi que tout autre champ que vous souhaitez définir |- |
| `delete` |Cette action supprime de l’index le document spécifié. |clé uniquement |Tous les champs que vous spécifiez en dehors du champ de clé sont ignorés. Si vous souhaitez supprimer un champ individuel dans un document, utilisez plutôt `merge` et définissez simplement le champ de manière explicite sur la valeur null. |

## <a name="construct-your-http-request-and-request-body"></a>Construire votre requête HTTP et le corps de la requête
Maintenant que vous avez recueilli les valeurs de champ requises pour les actions de votre index, vous pouvez construire votre requête HTTP et le corps de requête JSON pour importer vos données.

#### <a name="request-and-request-headers"></a>Requête et en-têtes de requête
Dans l’URL, vous devez fournir le nom de votre service, le nom de l’index (« hotels » dans notre exemple) ainsi que la version d’API appropriée (la version actuelle de l’API est celle du `2016-09-01` au moment de la publication de ce document). Vous devez définir les en-têtes de requête `Content-Type` et `api-key`. Pour cette dernière, utilisez l’une des clés d’administration de votre service.

    POST https://[search service].search.windows.net/indexes/hotels/docs/index?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

#### <a name="request-body"></a>Corps de la requête
```JSON
{
    "value": [
        {
            "@search.action": "upload",
            "hotelId": "1",
            "baseRate": 199.0,
            "description": "Best hotel in town",
            "description_fr": "Meilleur hôtel en ville",
            "hotelName": "Fancy Stay",
            "category": "Luxury",
            "tags": ["pool", "view", "wifi", "concierge"],
            "parkingIncluded": false,
            "smokingAllowed": false,
            "lastRenovationDate": "2010-06-27T00:00:00Z",
            "rating": 5,
            "location": { "type": "Point", "coordinates": [-122.131577, 47.678581] }
        },
        {
            "@search.action": "upload",
            "hotelId": "2",
            "baseRate": 79.99,
            "description": "Cheapest hotel in town",
            "description_fr": "Hôtel le moins cher en ville",
            "hotelName": "Roach Motel",
            "category": "Budget",
            "tags": ["motel", "budget"],
            "parkingIncluded": true,
            "smokingAllowed": true,
            "lastRenovationDate": "1982-04-28T00:00:00Z",
            "rating": 1,
            "location": { "type": "Point", "coordinates": [-122.131577, 49.678581] }
        },
        {
            "@search.action": "mergeOrUpload",
            "hotelId": "3",
            "baseRate": 129.99,
            "description": "Close to town hall and the river"
        },
        {
            "@search.action": "delete",
            "hotelId": "6"
        }
    ]
}
```

Dans ce cas, nous utilisons `upload`, `mergeOrUpload` et `delete` comme actions de recherche.

Supposons que cet exemple d’index « hotels » contient déjà un certain nombre de documents. Notez que nous n’avons pas eu à spécifier tous les champs de document possibles en utilisant `mergeOrUpload` et que nous n’avons fait que spécifier la clé de document (`hotelId`) avec la commande `delete`.

Notez également que chaque requête d’indexation ne peut contenir que 1 000  documents (ou 16 Mo) maximum.

## <a name="understand-your-http-response-code"></a>Comprendre votre code de réponse HTTP
#### <a name="200"></a>200
Lorsque votre demande d’indexation aboutit, vous recevez une réponse HTTP avec le code d’état `200 OK`. Le corps JSON de la réponse HTTP se présentera comme suit :

```JSON
{
    "value": [
        {
            "key": "unique_key_of_document",
            "status": true,
            "errorMessage": null
        },
        ...
    ]
}
```

#### <a name="207"></a>207
Un code d’état `207` est renvoyé lorsqu’au moins un élément n’a pas été correctement indexé. Le corps JSON de la réponse HTTP contient des informations sur les documents ayant échoué.

```JSON
{
    "value": [
        {
            "key": "unique_key_of_document",
            "status": false,
            "errorMessage": "The search service is too busy to process this document. Please try again later."
        },
        ...
    ]
}
```

> [!NOTE]
> Cela signifie généralement que la charge de votre service de recherche a atteint un point tel que les demandes d’indexation commencent à renvoyer des réponses `503`. Dans ce cas, nous vous recommandons vivement de désactiver votre code client et d’attendre avant d’effectuer une nouvelle tentative. En laissant au système le temps de récupérer, vous aurez davantage de chance de voir vos futures requêtes aboutir. Si vous renouvelez rapidement vos tentatives de requête, vous ne ferez que prolonger la situation.
>
>

#### <a name="429"></a>429
Le code d’état `429` est renvoyé lorsque vous avez dépassé votre quota de nombre de documents par index.

#### <a name="503"></a>503
Le code d’état `503` est renvoyé si aucun des éléments de la requête n’a été correctement indexé. Cette erreur signifie que le système est surchargé et que votre requête ne peut pas être traitée pour le moment.

> [!NOTE]
> Dans ce cas, nous vous recommandons vivement de désactiver votre code client et d’attendre avant d’effectuer une nouvelle tentative. En laissant au système le temps de récupérer, vous aurez davantage de chance de voir vos futures requêtes aboutir. Si vous renouvelez rapidement vos tentatives de requête, vous ne ferez que prolonger la situation.
>
>

Pour plus d’informations sur les actions de document et les réponses de réussite/d’erreur, consultez la page [Ajouter, mettre à jour ou supprimer des documents](https://docs.microsoft.com/rest/api/searchservice/AddUpdate-or-Delete-Documents). Pour plus d’informations sur les autres codes d’état HTTP pouvant être renvoyés en cas d’échec, consultez la page [Codes d’état HTTP (Azure Search)](https://docs.microsoft.com/rest/api/searchservice/HTTP-status-codes).

## <a name="next-steps"></a>Étapes suivantes
Une fois votre index Azure Search renseigné, vous pouvez commencer à exécuter des requêtes de recherche de documents. Pour plus d’informations, consultez l’article [Interroger votre index Azure Search](search-query-overview.md) .
