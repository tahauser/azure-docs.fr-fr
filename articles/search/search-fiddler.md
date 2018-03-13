---
title: Explorer les API REST de Fiddler ou Postman (REST de la Recherche Azure) | Microsoft Docs
description: "Découvrez comment émettre des demandes HTTP et des appels d’API REST vers la Recherche Azure, via Fiddler ou Postman."
services: search
documentationcenter: 
author: HeidiSteen
manager: cgronlun
editor: 
ms.assetid: 
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 01/04/2018
ms.author: heidist
ms.openlocfilehash: a9a6bc035ba9f02057e3086a9682b54282da19f3
ms.sourcegitcommit: e19f6a1709b0fe0f898386118fbef858d430e19d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/13/2018
---
# <a name="explore-azure-search-rest-apis-using-fiddler-or-postman"></a>Explorer les API REST de la Recherche Azure à l’aide de Fiddler ou Postman

L’une des méthodes les plus simples pour explorer les [API REST de la Recherche Azure](https://docs.microsoft.com/rest/api/searchservice) consiste à tirer parti de Fiddler ou Postman pour formuler les demandes HTTP et inspecter les réponses. Avec les bons outils et ces instructions, vous pouvez envoyer des requêtes et afficher les réponses avant d’écrire un code.

> [!div class="checklist"]
> * Télécharger un outil de test d’API web
> * Obtenir la clé d’API et le point de terminaison de votre service de recherche
> * Configurer des en-têtes de demande
> * Création d'un index
> * Charger un index
> * Rechercher dans un index

Si vous ne disposez d’aucun abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer, puis [inscrivez-vous auprès de la fonction Recherche Azure](search-create-service-portal.md).

## <a name="download-and-install-tools"></a>Télécharger et installer des outils

Les outils suivants sont souvent utilisés lors du développement web. Même si vous êtes familiarisé avec un autre outil, les instructions du présent article restent applicables, en théorie.

+ [Postman (complément Google Chrome)](https://www.getpostman.com/)
+ [Telerik Fiddler](http://www.telerik.com/fiddler)

## <a name="get-the-api-key-and-endpoint"></a>Obtenir la clé d’API et le point de terminaison

Les appels REST requièrent l’URL du service et une clé d’accès et ce, sur chaque demande. Un service de recherche est créé avec les deux. Ainsi, si vous avez ajouté votre abonnement à la fonction Recherche Azure, procédez comme suit pour obtenir les informations nécessaires :

1. Dans le portail Azure, ouvrez la page du service de recherche à partir du tableau de bord, ou [recherchez votre service](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) dans la liste.
2. Accédez au point de terminaison en cliquant sur **Vue d’ensemble** > **Essentials** > **URL**. Voici un exemple de point de terminaison : `https://my-service-name.search.windows.net`.
3. Obtenez la clé d’API en cliquant sur **Paramètres** > **Clés**. Il existe deux clés d’administration pour assurer la redondance, au cas où vous souhaiteriez substituer des clés. Les clés d’administration permettent d’accorder des autorisations d’accès en écriture à votre service, qui sont nécessaires pour la création et le chargement d’index. Vous pouvez utiliser la clé primaire ou secondaire pour vos opérations d’écriture.

## <a name="configure-request-headers"></a>Configurer des en-têtes de demande

Chaque outil conserve les informations d’en-tête de demande pour la session, ce qui signifie qu’il vous suffit d’indiquer une seule fois le point de terminaison d’URL, la version d’API, la clé d’API et le type de contenu.

L’adresse URL complète doit ressembler à l’exemple suivant. Toutefois, votre adresse URL doit proposer un remplacement valable pour le nom de l’espace réservé **`my-app`** :`https://my-app.search.windows.net/indexes/hotels?api-version=2016-09-01`

La composition de l’adresse URL du service inclut les éléments suivants :

+ préfixe HTTPS.
+ Adresse URL du service, obtenue à partir du portail.
+ Ressource, opération qui crée un objet sur votre service. Lors de cette étape, s’agit d’un index appelé hotels.
+ Version de l’API, chaîne en minuscules requise et spécifiée sous la forme « ?api-version=2016-09-01 » pour la version actuelle. [Les versions d’API](search-api-versions.md) sont régulièrement mises à jour. Le fait d’inclure la version d’API sur chaque demande vous permet de bénéficier du contrôle absolu sur la version utilisée.  

La composition d’en-tête de la demande inclut deux éléments, à savoir le type de contenu et la clé d’API décrite dans la section précédente :

         content-type: application/json
         api-key: <placeholder>

### <a name="fiddler"></a>Fiddler

Formulez une demande comparable à la capture d’écran suivante. Choisissez le verbe **PUT**. Fiddler ajoute la chaîne `User-Agent=Fiddler`. Vous pouvez coller les deux en-têtes de demande supplémentaires sur les nouvelles lignes en dessous. Incluez le type de contenu et la clé d’API de votre service, en utilisant la clé d’accès d’administrateur pour votre service.

![En-tête de demande Fiddler][1]

> [!Tip]
> Vous pouvez désactiver le trafic web pour masquer toute activité HTTP superflue qui n’est pas liée aux tâches que vous exécutez. Dans Fiddler, accédez au menu **Fichier** et désactivez l’option **Capturer le trafic**. 

### <a name="postman"></a>postman

Formulez une demande comparable à la capture d’écran suivante. Choisissez le verbe **PUT**. 

![En-tête de demande Postman][6]

## <a name="create-the-index"></a>Création de l'index

Le corps de la demande contient la définition de l’index. L’ajout d’un corps dans la demande de production de l’index termine cette dernière.

Outre le nom d’index, le composant le plus important de la demande correspond à la collection Fields. La collection Fields définit le schéma d’index. Dans chaque champ, spécifiez son type. Les champs de chaîne sont utilisés lors de recherches en texte intégral. Vous devrez peut-être transtyper les données numériques sous forme de chaînes, pour qu’il soit possible d’effectuer des recherches dans le contenu.

Les attributs du champ déterminent l’action autorisée. Les API REST autorisent de nombreuses actions par défaut. Par exemple, toutes les chaînes peuvent être soumises à des recherches, récupérables et filtrables et sont à choix multiples par défaut. Bien souvent, il suffit de définir des attributs lorsque vous devez désactiver un comportement. Pour en savoir plus sur les champs filtrables, consultez la section relative à la [création d’index (REST)](https://docs.microsoft.com/rest/api/searchservice/create-index).

          {
         "name": "hotels",  
         "fields": [
           {"name": "hotelId", "type": "Edm.String", "key":true, "searchable": false},
           {"name": "baseRate", "type": "Edm.Double"},
           {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
           {"name": "hotelName", "type": "Edm.String"},
           {"name": "category", "type": "Edm.String"},
           {"name": "tags", "type": "Collection(Edm.String)"},
           {"name": "parkingIncluded", "type": "Edm.Boolean"},
           {"name": "smokingAllowed", "type": "Edm.Boolean"},
           {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
           {"name": "rating", "type": "Edm.Int32"},
           {"name": "location", "type": "Edm.GeographyPoint"}
          ]
         }


Lorsque vous envoyez cette demande, vous devez obtenir une réponse HTTP 201, indiquant que l’index a bien été créé. Vous pouvez vérifier cette action dans le portail, mais n’oubliez pas que la page du portail est actualisée à intervalles réguliers. Il se peut donc que le résultat de l’action s’affiche au bout d’une minute ou deux.

Si vous obtenez HTTP 504, vérifiez que l'URL spécifie HTTPS. Si vous voyez HTTP 400 ou 404, contrôlez le corps de la demande pour vérifier l'absence d'erreurs de copier-coller. HTTP 403 indique normalement qu'il y a un problème avec la clé API (soit la clé n'est pas valide, soit il y a un problème de syntaxe avec la façon dont elle est spécifiée).

### <a name="fiddler"></a>Fiddler

Copiez la définition de l’index dans le corps de la demande, comme dans la capture d’écran suivante, puis cliquez sur l’option **Exécuter** affichée sur l’angle supérieur droit de la page pour envoyer la demande terminée.

![Corps de la demande Fiddler][7]

### <a name="postman"></a>postman

Copiez la définition de l’index dans le corps de la demande, comme dans la capture d’écran suivante, puis cliquez sur l’option **Envoyer** affichée sur l’angle supérieur droit de la page pour envoyer la demande terminée.

![Corps de la demande Postman][8]

## <a name="load-documents"></a>Chargement de documents

Les étapes de création et de remplissage d’index sont des opérations distinctes. Dans la fonction Recherche Azure, l’index contient toutes les données pouvant faire l’objet de recherches, que vous pouvez fournir sous forme de documents JSON. Pour vérifier l’API de cette opération, consultez la section relative à [l’ajout, la mise à jour ou la suppression de documents (REST)](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents).

+ Pour cette étape, remplacez le verbe par **POST**.
+ Modifiez le point de terminaison de façon à inclure `/docs/index`. L’URL complète doit ressembler à `https://my-app.search.windows.net/indexes/hotels/docs/index?api-version=2016-09-01`
+ Maintenez les en-têtes de demande en l’état. 

Le corps de la demande contient quatre documents à ajouter à l'index des hôtels.

         {
         "value": [
         {
             "@search.action": "upload",
             "hotelId": "1",
             "baseRate": 199.0,
             "description": "Best hotel in town",
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
             "@search.action": "upload",
             "hotelId": "3",
             "baseRate": 279.99,
             "description": "Surprisingly expensive",
             "hotelName": "Dew Drop Inn",
             "category": "Bed and Breakfast",
             "tags": ["charming", "quaint"],
             "parkingIncluded": true,
             "smokingAllowed": false,
             "lastRenovationDate": null,
             "rating": 4,
             "location": { "type": "Point", "coordinates": [-122.33207, 47.60621] }
           },
           {
             "@search.action": "upload",
             "hotelId": "4",
             "baseRate": 220.00,
             "description": "This could be the one",
             "hotelName": "A Hotel for Everyone",
             "category": "Basic hotel",
             "tags": ["pool", "wifi"],
             "parkingIncluded": true,
             "smokingAllowed": false,
             "lastRenovationDate": null,
             "rating": 4,
             "location": { "type": "Point", "coordinates": [-122.12151, 47.67399] }
           }
          ]
         }

Après quelques secondes, la réponse HTTP 200 apparaît dans la liste de sessions. Cela indique que les documents ont été correctement créés. 

Si vous obtenez HTTP 207, cela signifie qu'au moins un document n'a pas pu être chargé. Si l’erreur HTTP 404 s’affiche, cela signifie que vous avez fait une erreur de syntaxe dans l’en-tête ou le corps de la demande. Vérifiez que le point de terminaison a bien été modifié de manière à inclure `/docs/index`.

> [!Tip]
> Pour les sources de données sélectionnées, vous pouvez choisir l’approche *d’indexeur*, qui simplifie et réduit la quantité de code nécessaire pour l’indexation. Pour en savoir plus, consultez la section relative aux [opérations de l’indexeur](https://docs.microsoft.com/rest/api/searchservice/indexer-operations).

### <a name="fiddler"></a>Fiddler

Remplacez le verbe par **POST**. Modifiez l’URL pour inclure `/docs/index`. Copiez les documents dans le corps de la demande, comme dans la capture d’écran suivante, puis exécutez la requête.

![Charge utile de la demande Fiddler][9]

### <a name="postman"></a>postman

Remplacez le verbe par **POST**. Modifiez l’URL pour inclure `/docs/index`. Copiez les documents dans le corps de la demande, comme dans la capture d’écran suivante, puis exécutez la requête.

![En-tête de la demande Postman][10]

## <a name="query-the-index"></a>Interrogation de l'index
Maintenant qu'un index et des documents sont chargés, vous pouvez émettre des requêtes les concernant. Pour en savoir plus sur cette API, consultez la section relative à la [recherche de documents (REST)](https://docs.microsoft.com/rest/api/searchservice/search-documents)  

+ Pour cette étape, remplacez le verbe par **GET**.
+ Modifiez le point de terminaison de manière inclure les paramètres de requête, y compris les chaînes de recherche. Voici un exemple d’URL de requête : `https://my-app.search.windows.net/indexes/hotels/docs?search=motel&$count=true&api-version=2016-09-01`
+ Maintenez les en-têtes de demande en l’état

Cette requête effectue des recherches sur la base du mot « motel » et renvoie un certain nombre de documents dans les résultats de recherche. La demande et la réponse doivent ressembler à l’écran suivant Postman suivant, une fois que vous avez cliqué sur **Envoyer**. Le code d’état doit être 200.

 ![Réponse à la requête Postman][11]

### <a name="tips-for-running-our-sample-queries-in-fiddler"></a>Conseils relatifs à l’exécution de nos exemples de requêtes dans Fiddler

L’exemple de requête suivant provient de l’article relatif à [l’opération de recherche d’index (API de Recherche Azure)](http://msdn.microsoft.com/library/dn798927.aspx). Plusieurs exemples de requêtes de cette rubrique comportent des espaces, qui ne sont pas autorisés dans Fiddler. Remplacez chaque espace par un caractère + avant de coller la chaîne de requête et d’essayer la requête dans Fiddler.

**Avant le remplacement des espaces (dans la description de lastRenovationDate) :**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2016-09-01

**Avant le remplacement des espaces (dans lastRenovationDate+desc) :**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate+desc&api-version=2016-09-01

## <a name="query-index-properties"></a>Propriétés de l’index de requête
Vous pouvez également interroger les informations du système pour connaître le nombre de documents et l’espace de stockage utilisé : `https://my-app.search.windows.net/indexes/hotels/stats?api-version=2016-09-01`

Dans Postman, votre requête doit ressembler à ce qui suit ; la réponse inclut un nombre de documents et la quantité d’espace utilisé, en octets.

 ![Requête système Postman][12]

Notez que la syntaxe de la version d’API diffère. Pour cette requête, utilisez `?` pour ajouter la version d’API. Le point d’interrogation, ?, sépare le chemin d’accès de l’URL à partir de la chaîne de requête, alors que le signe & sépare chaque paire « nom=valeur » dans la chaîne de requête. Pour cette requête, la version d’API est le premier et seul élément de la chaîne de requête.

Pour en savoir plus sur cette API, consultez la section relative à [l’obtention des statistiques de l’index (REST)](https://docs.microsoft.com/rest/api/searchservice/get-index-statistics).


### <a name="tips-for-viewing-index-statistic-in-fiddler"></a>Conseils pour l’affichage des statistiques d’index dans Fiddler

Dans Fiddler, cliquez sur l’onglet **Inspecteurs**, sur l’onglet **En-têtes**, puis sélectionnez le format JSON. Le nombre de documents et la taille de stockage (en Ko) doivent s'afficher.

## <a name="next-steps"></a>Étapes suivantes

Les clients REST sont très utiles pour l’exploration ad hoc, mais vous connaissez le fonctionnement de l’API REST, donc vous pouvez avancer avec le code. Pour connaître les étapes suivantes, consultez les liens suivants :

+ [Création d’un index (REST)](search-create-index-rest-api.md)
+ [Importer des données (REST)](search-import-data-rest-api.md)
+ [Recherche d’index (REST)](search-query-rest-api.md)

<!--Image References-->
[1]: ./media/search-fiddler/fiddler-url.png
[2]: ./media/search-fiddler/AzureSearch_Fiddler2_PostDocs.png
[3]: ./media/search-fiddler/AzureSearch_Fiddler3_Query.png
[4]: ./media/search-fiddler/AzureSearch_Fiddler4_QueryResults.png
[5]: ./media/search-fiddler/AzureSearch_Fiddler5_QueryStats.png
[6]: ./media/search-fiddler/postman-url.png
[7]: ./media/search-fiddler/fiddler-request.png
[8]: ./media/search-fiddler/postman-request.png
[9]: ./media/search-fiddler/fiddler-docs.png
[10]: ./media/search-fiddler/postman-docs.png
[11]: ./media/search-fiddler/postman-query.png
[12]: ./media/search-fiddler/postman-system-query.png