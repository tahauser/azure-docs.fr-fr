---
title: 'Azure Cosmos DB : Comment interroger à l’aide de l’API MongoDB ? | Microsoft Docs'
description: Apprenez à interroger au moyen de l’API MongoDB pour Azure Cosmos DB
services: cosmos-db
documentationcenter: ''
author: mimig1
manager: jhubbard
editor: ''
tags: ''
ms.assetid: ''
ms.service: cosmos-db
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 03/16/2018
ms.author: mimig
ms.custom: mvc
ms.openlocfilehash: 7c51a2a1cace89305b971d5fb0f56c360cbf93cb
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/17/2018
---
# <a name="tutorial-query-azure-cosmos-db-by-using-the-mongodb-api"></a>Didacticiel : Interroger Azure Cosmos DB à l’aide de l’API MongoDB

[L’API pour MongoDB](mongodb-introduction.md) d’Azure Cosmos DB prend en charge les [requêtes shell MongoDB](https://docs.mongodb.com/manual/tutorial/query-documents/). 

Cet article décrit les tâches suivantes : 

> [!div class="checklist"]
> * Interrogation des données avec MongoDB

## <a name="sample-document"></a>Exemple de document

L’exemple de document suivant est utilisé pour les requêtes de cet article.

```json
{
  "id": "WakefieldFamily",
  "parents": [
      { "familyName": "Wakefield", "givenName": "Robin" },
      { "familyName": "Miller", "givenName": "Ben" }
  ],
  "children": [
      {
        "familyName": "Merriam", 
        "givenName": "Jesse", 
        "gender": "female", "grade": 1,
        "pets": [
            { "givenName": "Goofy" },
            { "givenName": "Shadow" }
        ]
      },
      { 
        "familyName": "Miller", 
         "givenName": "Lisa", 
         "gender": "female", 
         "grade": 8 }
  ],
  "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
  "creationDate": 1431620462,
  "isRegistered": false
}
```
## <a id="examplequery1"></a> Exemple de requête 1 

Étant donné l’exemple de document de famille ci-dessus, la requête suivante renvoie les documents où le champ id correspond à `WakefieldFamily`.

**Requête**
    
    db.families.find({ id: “WakefieldFamily”})

**Résultats**

    {
    "_id": "ObjectId(\"58f65e1198f3a12c7090e68c\")",
    "id": "WakefieldFamily",
    "parents": [
      {
        "familyName": "Wakefield",
        "givenName": "Robin"
      },
      {
        "familyName": "Miller",
        "givenName": "Ben"
      }
    ],
    "children": [
      {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [
          { "givenName": "Goofy" },
          { "givenName": "Shadow" }
        ]
      },
      {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
      }
    ],
    "address": {
      "state": "NY",
      "county": "Manhattan",
      "city": "NY"
    },
    "creationDate": 1431620462,
    "isRegistered": false
    }

## <a id="examplequery2"></a>Exemple de requête 2 

La requête suivante renvoie tous les enfants de la famille. 

**Requête**
    
    db.families.find( { id: “WakefieldFamily” }, { children: true } )

**Résultats**

    {
    "_id": "ObjectId("58f65e1198f3a12c7090e68c")",
    "children": [
      {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [
          { "givenName": "Goofy" },
          { "givenName": "Shadow" }
        ]
      },
      {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
      }
    ]
    }


## <a id="examplequery3"></a>Exemple de requête 3 

La requête suivante renvoie toutes les familles qui sont enregistrées. 

**Requête**
    
    db.families.find( { "isRegistered" : true })
**Résultats** Aucun document n’est renvoyé. 

## <a id="examplequery4"></a>Exemple de requête 4

La requête suivante renvoie toutes les familles qui ne sont pas enregistrées. 

**Requête**
    
    db.families.find( { "isRegistered" : false })
**Résultats**

     {
    "_id": ObjectId("58f65e1198f3a12c7090e68c"),
    "id": "WakefieldFamily",
    "parents": [{
        "familyName": "Wakefield",
        "givenName": "Robin"
    }, {
        "familyName": "Miller",
        "givenName": "Ben"
    }],
    "children": [{
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [{
            "givenName": "Goofy"
        }, {
            "givenName": "Shadow"
        }]
    }, {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
    }],
    "address": {
        "state": "NY",
        "county": "Manhattan",
        "city": "NY"
    },
    "creationDate": 1431620462,
    "isRegistered": false
}

## <a id="examplequery5"></a>Exemple de requête 5

La requête suivante renvoie toutes les familles qui ne sont pas enregistrées dans l’état de New-York (NY). 

**Requête**
    
     db.families.find( { "isRegistered" : false, "address.state" : "NY" })

**Résultats**

     {
    "_id": ObjectId("58f65e1198f3a12c7090e68c"),
    "id": "WakefieldFamily",
    "parents": [{
        "familyName": "Wakefield",
        "givenName": "Robin"
    }, {
        "familyName": "Miller",
        "givenName": "Ben"
    }],
    "children": [{
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [{
            "givenName": "Goofy"
        }, {
            "givenName": "Shadow"
        }]
    }, {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
    }],
    "address": {
        "state": "NY",
        "county": "Manhattan",
        "city": "NY"
    },
    "creationDate": 1431620462,
    "isRegistered": false
}


## <a id="examplequery6"></a>Exemple de requête 6

La requête suivante renvoie toutes les familles dans lesquelles les enfants sont en 8e année.

**Requête**
  
     db.families.find( { children : { $elemMatch: { grade : 8 }} } )

**Résultats**

     {
    "_id": ObjectId("58f65e1198f3a12c7090e68c"),
    "id": "WakefieldFamily",
    "parents": [{
        "familyName": "Wakefield",
        "givenName": "Robin"
    }, {
        "familyName": "Miller",
        "givenName": "Ben"
    }],
    "children": [{
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [{
            "givenName": "Goofy"
        }, {
            "givenName": "Shadow"
        }]
    }, {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
    }],
    "address": {
        "state": "NY",
        "county": "Manhattan",
        "city": "NY"
    },
    "creationDate": 1431620462,
    "isRegistered": false
}

## <a id="examplequery7"></a>Exemple de requête 7

La requête suivante renvoie toutes les familles dont le nombre d’enfants est 3.

**Requête**
  
      db.Family.find( {children: { $size:3} } )

**Résultats**

Aucun résultat n’est renvoyé car aucune n’a plus de 2 enfants. Cette requête ne permet d’obtenir un résultat que lorsque le paramètre est 2, auquel cas l’ensemble du document est renvoyé.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Effectuer des interrogations à l’aide de MongoDB 

Vous pouvez maintenant poursuivre avec le didacticiel suivant montrant comment distribuer vos données globalement.

> [!div class="nextstepaction"]
> [Distribuer vos données globalement](tutorial-global-distribution-sql-api.md)

