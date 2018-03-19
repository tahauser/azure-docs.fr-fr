---
title: "Indexation dans l’API MongoDB Azure Cosmos DB | Microsoft Docs"
description: "Présente une vue d’ensemble des fonctionnalités d’indexation dans l’API MongoDB Azure Cosmos DB."
services: cosmos-db
documentationcenter: 
author: orestis-ms
manager: jhubbard
editor: 
ms.assetid: daacbabf-1bb5-497f-92db-079910703047
ms.service: cosmos-db
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: quickstart
ms.date: 03/01/2018
ms.author: orkostak
ms.openlocfilehash: 090f8a664409d9cde1e4440ca9e2390a7c9def7a
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="indexing-in-the-azure-cosmos-db-mongodb-api"></a>Indexation dans Azure Cosmos DB : API MongoDB

L’API MongoDB Azure Cosmos DB tire parti des fonctionnalités de gestion automatique des index d’Azure Cosmos DB. Par conséquent, les utilisateurs ont accès aux stratégies d’indexation par défaut d’Azure Cosmos DB. Ainsi, si aucun index n’a été défini par l’utilisateur ou n’a été supprimé, tous les champs seront automatiquement indexés par défaut lorsqu’ils seront insérés dans la collection. Pour la plupart des scénarios, nous vous recommandons d’utiliser la stratégie d’indexation par défaut définie sur le compte.

## <a name="dropping-the-default-indexes"></a>Suppression des index par défaut

La commande suivante peut être utilisée pour supprimer les index par défaut d’une collection ```coll```:

```JavaScript
> db.coll.dropIndexes()
{ "_t" : "DropIndexesResponse", "ok" : 1, "nIndexesWas" : 3 }
```

## <a name="creating-compound-indexes"></a>Création d’index composés

Les index composés comportent des références à plusieurs champs d’un document. Logiquement, ils équivalent à la création de plusieurs index individuels par champ. Pour tirer parti des optimisations fournies par les techniques d’indexation de Cosmos DB, nous vous recommandons de créer plusieurs index individuels au lieu d’un seul index composé (non unique).

## <a name="creating-unique-indexes"></a>Création d’index uniques

[Les index uniques](unique-keys.md) sont utiles pour s’assurer que deux documents ou plus ne contiennent pas la même valeur pour les champs indexés. 
>[!important] 
> Actuellement, les index uniques ne peuvent être créés que lorsque la collection est vide (lorsqu’elle ne contient aucun document). 

La commande suivante crée un index unique sur le champ « student_id » :

```JavaScript
globaldb:PRIMARY> db.coll.createIndex( { "student_id" : 1 }, {unique:true} ) 
{
        "_t" : "CreateIndexesResponse",
        "ok" : 1,
        "createdCollectionAutomatically" : false,
        "numIndexesBefore" : 1,
        "numIndexesAfter" : 4
}
```

Pour les collections partitionnées, en fonction du comportement de MongoDB, la création d’un index unique nécessite la clé de partition. En d’autres termes, tous les index uniques dans une collection partitionnée sont des index composés où l’un des champs est la clé de partition.

Les commandes suivantes créent une collection partitionnée ```coll``` (la clé de partition est ```university```) avec un index unique sur les champs student_id et university :

```JavaScript
globaldb:PRIMARY> db.runCommand({shardCollection: db.coll._fullName, key: { university: "hashed"}});
{
        "_t" : "ShardCollectionResponse",
        "ok" : 1,
        "collectionsharded" : "test.coll"
}
globaldb:PRIMARY> db.coll.createIndex( { "student_id" : 1, "university" : 1 }, {unique:true})
{
        "_t" : "CreateIndexesResponse",
        "ok" : 1,
        "createdCollectionAutomatically" : false,
        "numIndexesBefore" : 3,
        "numIndexesAfter" : 4
}
```

Dans l’exemple ci-dessus, si la clause ```"university":1``` a été omise, une erreur avec le message suivant est renvoyée :

```"cannot create unique index over {student_id : 1.0} with shard key pattern { university : 1.0 }"```

## <a name="ttl-indexes"></a>Index TTL

Pour activer l’expiration de document dans une collection particulière, un [« index TTL » (index de durée de vie)](../cosmos-db/time-to-live.md) doit être créé. Un index TTL est un index sur le champ _ts avec une valeur « expireAfterSeconds ».
 
Exemple :
```JavaScript
globaldb:PRIMARY> db.coll.createIndex({"_ts":1}, {expireAfterSeconds: 10})
```

La commande précédente entraîne la suppression de tous les documents de la collection ```db.coll``` qui n’ont pas été modifiés au cours des 10 dernières secondes. 
 
> [!NOTE]
> **_ts** est un champ spécifique de Cosmos DB qui n’est pas accessible à partir des clients MongoDB. Il s’agit d’une propriété (système) réservée qui contient le timestamp de la dernière modification du document.
>

## <a name="migrating-collections-with-indexes"></a>Migration de collections avec des index

Actuellement, la création d’index uniques n’est possible que lorsque la collection ne contient aucun document. Les outils de migration MongoDB populaires tentent de créer des index uniques après l’importation des données. Pour contourner ce problème, il est recommandé aux utilisateurs de créer manuellement les collections et index uniques correspondants, au lieu de laisser l’outil de migration le faire (pour ```mongorestore``` ce comportement est obtenu à l’aide de l’indicateur--noIndexRestore dans la ligne de commande).

## <a name="next-steps"></a>Étapes suivantes
* [Comment Azure Cosmos DB indexe-t-il les données ?](../cosmos-db/indexing-policies.md)
* [Faire expirer des données dans des collections Azure Cosmos DB automatiquement avec la durée de vie](../cosmos-db/time-to-live.md)
