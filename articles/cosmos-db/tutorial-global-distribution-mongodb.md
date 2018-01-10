---
title: "Didacticiel de diffusion mondiale d’Azure Cosmos DB pour l’API MongoDB | Documents Microsoft"
description: "Découvrez comment configurer la diffusion mondiale d’Azure Cosmos DB à l’aide de l’API MongoDB."
services: cosmos-db
keywords: diffusion mondiale, MongoDB
documentationcenter: 
author: mimig1
manager: jhubbard
editor: cgronlun
ms.assetid: 8b815047-2868-4b10-af1d-40a1af419a70
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/10/2017
ms.author: mimig
ms.custom: mvc
ms.openlocfilehash: d051c648ac66a42cefe0113d2571fe0a3050a237
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="how-to-setup-azure-cosmos-db-global-distribution-using-the-mongodb-api"></a>Comment configurer la diffusion mondiale d’Azure Cosmos DB à l’aide de l’API MongoDB

Dans cet article, nous vous montrons comment utiliser le portail Azure pour configurer la diffusion mondiale d’Azure Cosmos DB, puis établir une connexion à l’aide de l’API MongoDB.

Cet article décrit les tâches suivantes : 

> [!div class="checklist"]
> * Configurer la diffusion mondiale à l’aide du portail Azure
> * Configurer la diffusion mondiale à l’aide de [l’API MongoDB](mongodb-introduction.md)

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]

## <a name="verifying-your-regional-setup-using-the-mongodb-api"></a>Vérification de votre configuration régionale avec l’API MongoDB
La façon la plus simple de vérifier votre configuration globale au sein de l’API pour MongoDB consiste à exécuter la commande *isMaster()* à partir de l’interpréteur de commandes Mongo.

À partir de votre interpréteur de commandes Mongo :

   ```
      db.isMaster()
   ```
   
Résultats de l’exemple :

   ```JSON
      {
         "_t": "IsMasterResponse",
         "ok": 1,
         "ismaster": true,
         "maxMessageSizeBytes": 4194304,
         "maxWriteBatchSize": 1000,
         "minWireVersion": 0,
         "maxWireVersion": 2,
         "tags": {
            "region": "South India"
         },
         "hosts": [
            "vishi-api-for-mongodb-southcentralus.documents.azure.com:10255",
            "vishi-api-for-mongodb-westeurope.documents.azure.com:10255",
            "vishi-api-for-mongodb-southindia.documents.azure.com:10255"
         ],
         "setName": "globaldb",
         "setVersion": 1,
         "primary": "vishi-api-for-mongodb-southindia.documents.azure.com:10255",
         "me": "vishi-api-for-mongodb-southindia.documents.azure.com:10255"
      }
   ```

## <a name="connecting-to-a-preferred-region-using-the-mongodb-api"></a>Connexion à une région de prédilection avec l’API MongoDB

L’API MongoDB vous permet de spécifier les préférences de lecture de votre collection pour une base de données mondialement diffusée. Pour les lectures à faible latence et la haute disponibilité globale, nous vous recommandons de définir les préférences de lecture de votre collection sur *La plus proche*. Une préférence de lecture définie sur *La plus proche* est configurée pour lire à partir de la région la plus proche.

```csharp
var collection = database.GetCollection<BsonDocument>(collectionName);
collection = collection.WithReadPreference(new ReadPreference(ReadPreferenceMode.Nearest));
```

Pour les applications avec une région primaire en lecture/écriture et une région secondaire pour les scénarios de récupération d’urgence, nous vous recommandons de définir les préférences de lecture de votre collection sur *Secondary preferred* (Secondaire par défaut). Une préférence de lecture définie sur *Secondary preferred* (Secondaire par défaut) est configurée pour lire à partir de la région secondaire lorsque la région primaire n’est pas disponible.

```csharp
var collection = database.GetCollection<BsonDocument>(collectionName);
collection = collection.WithReadPreference(new ReadPreference(ReadPreferenceMode.SecondaryPreferred));
```

Enfin, si vous souhaitez spécifier manuellement vos régions de lecture, vous pouvez définir la balise de la région dans vos préférences de lecture.

```csharp
var collection = database.GetCollection<BsonDocument>(collectionName);
var tag = new Tag("region", "Southeast Asia");
collection = collection.WithReadPreference(new ReadPreference(ReadPreferenceMode.Secondary, new[] { new TagSet(new[] { tag }) }));
```

C’est ici que s’achève ce didacticiel. Découvrez comment gérer la cohérence de votre compte répliqué à l’échelle mondiale en lisant l’article [Niveaux de cohérence dans Azure Cosmos DB](consistency-levels.md). Pour plus d’informations sur le fonctionnement de la réplication de base de données à l’échelle mondiale dans Azure Cosmos DB, voir [Diffuser des données à l’échelle mondiale avec Azure Cosmos DB](distribute-data-globally.md).

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez effectué les tâches suivantes :

> [!div class="checklist"]
> * Configurer la distribution mondiale à l’aide du portail Azure
> * Configurer la distribution mondiale à l’aide des API SQL

Vous pouvez maintenant passer au didacticiel suivant pour apprendre à développer en local à l’aide de l’émulateur local Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Développer en local avec l’émulateur](local-emulator.md)
