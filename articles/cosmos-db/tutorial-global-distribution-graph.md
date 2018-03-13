---
title: "Didacticiel de distribution mondiale Azure Cosmos DB pour l’API Graph | Microsoft Docs"
description: "Découvrez comment configurer la distribution mondiale Azure Cosmos DB à l’aide de l’API Graph."
services: cosmos-db
keywords: distribution globale, graph, gremlin
documentationcenter: 
author: luisbosquez
manager: jhubbard
editor: cgronlun
ms.assetid: 8b815047-2868-4b10-af1d-40a1af419a70
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/02/2018
ms.author: lbosq
ms.custom: mvc
ms.openlocfilehash: 1806bde383f04747f1f0fef46e5cf4d38de1e939
ms.sourcegitcommit: 9ea2edae5dbb4a104322135bef957ba6e9aeecde
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/03/2018
---
# <a name="how-to-setup-azure-cosmos-db-global-distribution-using-the-graph-api"></a>Comment configurer la distribution mondiale Azure Cosmos DB à l’aide de l’API Graph

Dans cet article, nous allons vous montrer comment utiliser le portail Azure pour configurer la distribution mondiale Azure Cosmos DB avant d’établir une connexion à l’aide de l’API Graph.

Cet article décrit les tâches suivantes : 

> [!div class="checklist"]
> * Configurer la diffusion mondiale à l’aide du portail Azure
> * Configurer la distribution mondiale à l’aide des [API Graph](graph-introduction.md)

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]


## <a name="connecting-to-a-preferred-region-using-the-graph-api-using-the-net-sdk"></a>Se connecter à une région de prédilection avec l’API Graph à l’aide du SDK .NET

L’API Graph est exposée sous forme de bibliothèque d’extension sur l’API SQL.

Pour tirer parti de la [distribution mondiale](distribute-data-globally.md), les applications clientes peuvent spécifier la liste ordonnée de préférences de régions à utiliser pour effectuer des opérations sur les documents. Pour cela, vous devez configurer la stratégie de connexion. Selon la configuration du compte Azure Cosmos DB, la disponibilité régionale actuelle et la liste de préférences spécifiée, le Kit de développement logiciel (SDK) choisit le point de terminaison optimal pour les opérations de lecture et d’écriture.

Cette liste de préférences est spécifiée lors de l’initialisation d’une connexion à l’aide des SDK. Les SDK acceptent un paramètre facultatif « PreferredLocations » qui est une liste ordonnée des régions Azure.

* **Écritures** : le SDK envoie automatiquement toutes les écritures vers la région d’écriture active.
* **Lectures** : toutes les lectures sont envoyées vers la première région disponible dans la liste PreferredLocations. Si la requête échoue, le client passe à la région suivante dans la liste, et ainsi de suite. Les SDK tentent des opérations de lecture uniquement à partir des régions spécifiées dans PreferredLocations. Ainsi, par exemple, si le compte Cosmos DB est disponible dans trois régions, mais que le client spécifie uniquement deux des régions sans écriture de PreferredLocations, aucune lecture n’est traitée hors de la région d’écriture, même en cas de basculement.

L’application peut vérifier le point de terminaison d’écriture et le point de terminaison de lecture actuels choisis par le SDK en vérifiant deux propriétés, WriteEndpoint et ReadEndpoint, disponibles dans le SDK version 1.8 et ultérieure. Si la propriété PreferredLocations n’est pas définie, toutes les requêtes sont traitées par la zone d’écriture active.

### <a name="using-the-sdk"></a>Utilisation du kit de développement logiciel

Par exemple, dans le SDK .NET, le paramètre `ConnectionPolicy` du constructeur `DocumentClient` comporte une propriété appelée `PreferredLocations`. Cette propriété peut être définie sur une liste de noms de région. Le noms d’affichage des [régions Azure][regions] peuvent être spécifié dans `PreferredLocations`.

> [!NOTE]
> Les URL des points de terminaison ne doivent pas être considérées comme des constantes à long terme. Le service peut les mettre à jour à tout moment. Le SDK gère ce changement automatiquement.
>
>

```cs
// Getting endpoints from application settings or other configuration location
Uri accountEndPoint = new Uri(Properties.Settings.Default.GlobalDatabaseUri);
string accountKey = Properties.Settings.Default.GlobalDatabaseKey;

ConnectionPolicy connectionPolicy = new ConnectionPolicy();

//Setting read region selection preference
connectionPolicy.PreferredLocations.Add(LocationNames.WestUS); // first preference
connectionPolicy.PreferredLocations.Add(LocationNames.EastUS); // second preference
connectionPolicy.PreferredLocations.Add(LocationNames.NorthEurope); // third preference

// initialize connection
DocumentClient docClient = new DocumentClient(
    accountEndPoint,
    accountKey,
    connectionPolicy);

// connect to Azure Cosmos DB
await docClient.OpenAsync().ConfigureAwait(false);
```

C’est ici que s’achève ce didacticiel. Découvrez comment gérer la cohérence de votre compte répliqué à l’échelle mondiale en lisant l’article [Niveaux de cohérence dans Azure Cosmos DB](consistency-levels.md). Pour plus d’informations sur le fonctionnement de la réplication de base de données à l’échelle mondiale dans Azure Cosmos DB, voir [Diffuser des données à l’échelle mondiale avec Azure Cosmos DB](distribute-data-globally.md).

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez :

> [!div class="checklist"]
> * Configurer la diffusion mondiale à l’aide du portail Azure
> * Configurer la distribution mondiale à l’aide des API SQL

Vous pouvez maintenant passer au didacticiel suivant pour apprendre à développer en local à l’aide de l’émulateur local Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Développer en local avec l’émulateur](local-emulator.md)

[regions]: https://azure.microsoft.com/regions/

