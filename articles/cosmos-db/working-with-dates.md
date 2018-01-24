---
title: "Utilisation des dates dans Azure Cosmos DB | Microsoft Docs"
description: "En savoir plus sur l’utilisation des dates dans Azure Cosmos DB."
services: cosmos-db
author: arramac
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: e587772f-ce9f-498c-a017-a51e7265bb23
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/25/2017
ms.author: arramac
ms.openlocfilehash: 1a54884196e5b4ff5b16425e902abeb8d82aa8f1
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2017
---
# <a name="working-with-dates-in-azure-cosmos-db"></a>Utilisation des dates dans Azure Cosmos DB
Azure Cosmos DB offre une flexibilité des schémas et une indexation riche par le biais d’un modèle de données [JSON](http://www.json.org) natif. Toutes les ressources Azure Cosmos DB, y compris les bases de données, les collections, les documents et les procédures stockées, sont modélisées et stockées en tant que documents JSON. Pour être portable, JSON (et Azure Cosmos DB) prend en charge uniquement un petit ensemble de types de base : chaîne, nombre, booléen, tableau, objet et null. Toutefois, JSON est flexible et permet aux développeurs et aux infrastructures de représenter des types plus complexes en utilisant ces primitives et en les composant en tant qu’objets ou tableaux. 

Outre les types de base, de nombreuses applications ont besoin du type [DateTime](https://msdn.microsoft.com/library/system.datetime(v=vs.110).aspx) pour représenter des dates et horodatages. Cet article décrit comment des développeurs peuvent stocker, récupérer et interroger des dates dans Azure Cosmos DB à l’aide du kit SDK .NET.

## <a name="storing-datetimes"></a>Stockage de valeurs DateTime
Par défaut, le [SDK Azure Cosmos DB](sql-api-sdk-dotnet.md) sérialise les valeurs DateTime en tant que chaînes [ISO 8601](http://www.iso.org/iso/catalogue_detail?csnumber=40874). La plupart des applications peuvent utiliser la représentation sous forme de chaîne par défaut pour DateTime pour les raisons suivantes :

* Les chaînes peuvent être comparées et l’ordre relatif des valeurs DateTime est conservé lorsque celles-ci sont transformées en chaînes. 
* Cette approche ne nécessite pas de code personnalisé ou d’attributs pour la conversion JSON.
* Les dates stockées au format JSON sont lisibles.
* Cette approche peut tirer parti de l’index d’Azure Cosmos DB pour offrir des performances de requête rapides.

Par exemple, l’extrait de code suivant stocke un objet `Order` contenant deux propriétés DateTime (`ShipDate` et `OrderDate`) en tant que document à l’aide du SDK .NET :

    public class Order
    {
        [JsonProperty(PropertyName="id")]
        public string Id { get; set; }
        public DateTime OrderDate { get; set; }
        public DateTime ShipDate { get; set; }
        public double Total { get; set; }
    }

    await client.CreateDocumentAsync("/dbs/orderdb/colls/orders", 
        new Order 
        { 
            Id = "09152014101",
            OrderDate = DateTime.UtcNow.AddDays(-30),
            ShipDate = DateTime.UtcNow.AddDays(-14), 
            Total = 113.39
        });

Ce document est stocké dans Azure Cosmos DB comme suit :

    {
        "id": "09152014101",
        "OrderDate": "2014-09-15T23:14:25.7251173Z",
        "ShipDate": "2014-09-30T23:14:25.7251173Z",
        "Total": 113.39
    }
    

Vous pouvez également stocker les valeurs DateTime comme horodateurs Unix, autrement dit, comme un nombre représentant le nombre de secondes écoulées depuis le 1er janvier 1970. La propriété Timestamp interne d’Azure Cosmos DB (`_ts`) suit cette approche. Vous pouvez utiliser la classe [UnixDateTimeConverter](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.unixdatetimeconverter.aspx) pour sérialiser les valeurs DateTime en tant que nombres. 

## <a name="indexing-datetimes-for-range-queries"></a>Indexation des valeurs DateTime pour les requêtes de plage
Les requêtes de plage ont souvent des valeurs DateTime. Par exemple, si vous avez besoin de rechercher toutes les commandes créées depuis la veille ou de rechercher toutes les commandes expédiées au cours des cinq dernières minutes, vous devez effectuer des requêtes de plage. Pour exécuter ces requêtes efficacement, vous devez configurer votre collection pour l’indexation de plage sur les chaînes.

    DocumentCollection collection = new DocumentCollection { Id = "orders" };
    collection.IndexingPolicy = new IndexingPolicy(new RangeIndex(DataType.String) { Precision = -1 });
    await client.CreateDocumentCollectionAsync("/dbs/orderdb", collection);

Pour plus d’informations sur la configuration des stratégies d’indexation, consultez [Stratégies d’indexation d’Azure Cosmos DB](indexing-policies.md).

## <a name="querying-datetimes-in-linq"></a>Interrogation des valeurs DateTime dans LINQ
Le SDK .NET SQL prend automatiquement en charge l’interrogation des données stockées dans Azure Cosmos DB par le biais de LINQ. Par exemple, l’extrait de code suivant montre une requête LINQ qui filtre les commandes qui ont été expédiées au cours des trois derniers jours.

    IQueryable<Order> orders = client.CreateDocumentQuery<Order>("/dbs/orderdb/colls/orders")
        .Where(o => o.ShipDate >= DateTime.UtcNow.AddDays(-3));
          
    // Translated to the following SQL statement and executed on Azure Cosmos DB
    SELECT * FROM root WHERE (root["ShipDate"] >= "2016-12-18T21:55:03.45569Z")

Pour plus d’informations sur le langage de requête SQL d’Azure Cosmos DB et le fournisseur LINQ, consultez [Interrogation de Cosmos DB](sql-api-sql-query.md).

Dans cet article, nous avons vu comment stocker, indexer et interroger les valeurs DateTime dans Azure Cosmos DB.

## <a name="next-steps"></a>Étapes suivantes
* Télécharger et exécuter les [exemples de code sur GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples)
* En savoir plus sur les [requêtes SQL](sql-api-sql-query.md)
* En savoir plus sur les [stratégies d’indexation Azure Cosmos DB](indexing-policies.md)
