---
title: Surveillance et débogage à l’aide de métriques dans Azure Cosmos DB | Microsoft Docs
description: Utilisez les métriques d’Azure Cosmos DB pour déboguer les problèmes courants et surveiller la base de données.
keywords: Mesures
services: cosmos-db
author: gnot
manager: jhubbard
editor: ''
documentationcenter: ''
ms.assetid: ''
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: govindk
ms.openlocfilehash: 37ef1bc01bd3679e664e71e6a6e3bede84615305
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2018
---
# <a name="monitoring-and-debugging-with-metrics-in-azure-cosmos-db"></a>Surveillance et débogage à l’aide de métriques dans Azure Cosmos DB

Azure Cosmos DB fournit des métriques concernant le débit, le stockage, la cohérence, la disponibilité et la latence. Le [portail Azure](https://portal.azure.com) fournit une vue agrégée de ces métriques. Pour obtenir une vue détaillée des métriques, consultez le SDK du client et les [journaux de diagnostic](./logging.md).

Pour obtenir une vue d’ensemble des nouvelles métriques et savoir comment trouver les partitions les plus utilisées de votre base de données, regardez la vidéo Azure Friday suivante :

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Azure-Cosmos-DB-Get-the-Most-Out-of-Provisioned-Throughput/player]
> 

Cet article explique des cas d’utilisation courants et montre comment utiliser les métriques d’Azure Cosmos DB pour analyser et déboguer ces problèmes. Les métriques sont collectées toutes les cinq minutes et sont conservées pendant sept jours.

## <a name="understanding-how-many-requests-are-succeeding-or-causing-errors"></a>Connaître le nombre de requêtes ayant abouti ou ayant provoqué une erreur

Pour commencer, accédez au [portail Azure](https://portal.azure.com), puis accédez au panneau **Métriques**. Dans le panneau, recherchez le graphique **Nombre de requêtes ayant dépassé la capacité par tranche de 1 minute**. Ce graphique montre, minute par minute, le nombre total de requêtes, segmentées par code d’état. Pour plus d’informations sur les codes d’état HTTP, consultez [Codes d’état HTTP pour Azure Cosmos DB](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb).

Le code d’état d’erreur le plus commun est le code 429 (erreur de limitation), qui indique que les requêtes envoyées à Azure Cosmos DB dépassent le débit approvisionné. Dans ce cas, la solution la plus courante consiste à [effectuer une montée en puissance des unités de requête](./set-throughput.md) pour une collection donnée.

![Nombre de requêtes par minute](media/use-metrics/metrics-12.png)

## <a name="determining-the-throughput-distribution-across-partitions"></a>Détermination de la distribution du débit entre les partitions

Il est essentiel d’avoir une bonne cardinalité des clés de partition pour vos applications évolutives. Pour déterminer la distribution du débit au sein d’une collection partitionnée, accédez au **panneau Métriques** dans le [portail Azure](https://portal.azure.com). Sous l’onglet **Débit**, la répartition du débit est affichée dans le graphique **Nombre maximal de RU/seconde consommées par chaque partition physique**. Le graphique suivant montre un exemple de mauvaise distribution des données mise en évidence par l’asymétrie de la partition située à l’extrême gauche. 

![Partition fortement utilisée à 15 h 05](media/use-metrics/metrics-17.png)

Une distribution inégale du débit peut aboutir à une *forte utilisation* de certaines partitions. Dans ce cas, une limitation des requêtes peut se produire et nécessiter un repartitionnement. Pour plus d’informations sur le partitionnement dans Azure Cosmos DB, consultez [Partitionner et mettre à l’échelle dans Azure Cosmos DB](./partition-data.md).

## <a name="determining-the-storage-distribution-across-partitions"></a>Détermination de la distribution du stockage entre les partitions

Il est essentiel d’avoir une bonne cardinalité de partition pour vos applications évolutives. Pour déterminer la distribution du stockage au sein d’une collection partitionnée, accédez au panneau Métriques dans le [portail Azure](https://portal.azure.com). Sous l’onglet Stockage, la répartition du stockage est affichée dans le graphique Nombre maximal de RU/seconde consommées par chaque partition physique. Le graphique suivant montre une mauvaise distribution des données mise en évidence par l’asymétrie de la partition située à l’extrême gauche. 

![Exemple de mauvaise distribution des données](media/use-metrics/metrics-07.png)

Vous pouvez connaître la clé de partition à l’origine du déséquilibre de la distribution en cliquant sur la partition du graphique. 

![Clé de partition provoquant un déséquilibre de la distribution](media/use-metrics/metrics-05.png)

Une fois que vous avez identifié la clé de partition qui est à l’origine du déséquilibre, il est possible que vous deviez repartitionner votre collection avec une clé de partition mieux distribuée. Pour plus d’informations sur le partitionnement dans Azure Cosmos DB, consultez [Partitionner et mettre à l’échelle dans Azure Cosmos DB](./partition-data.md).

## <a name="comparing-data-size-against-index-size"></a>Comparaison entre la taille des données et la taille de l’index

Dans Azure Cosmos DB, la consommation totale du stockage correspond à la somme de la taille des données et de la taille de l’index. En règle générale, la taille de l’index correspond à une fraction de la taille des données. Dans le panneau Métriques du [portail Azure](https://portal.azure.com), l’onglet Stockage présente la répartition de la consommation du stockage entre les données et l’index. Vous pouvez également connaître l’utilisation actuelle du stockage dans le SDK, au moyen d’une lecture de collection.
```csharp
// Measure the document size usage (which includes the index size)  
ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll")); 
 Console.WriteLine("Document size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);
``` 
Si vous souhaitez conserver de l’espace d’index, vous pouvez ajuster la [stratégie d’indexation](./indexing-policies.md).

## <a name="debugging-why-queries-are-running-slow"></a>Résolution des problèmes liés à l’exécution lente des requêtes

Dans les SDK de l’API SQL, Azure Cosmos DB fournit des statistiques relatives à l’exécution des requêtes. 

```csharp
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
 UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName), 
 “SELECT * FROM c WHERE c.city = ‘Seattle’”, 
 new FeedOptions 
 { 
 PopulateQueryMetrics = true, 
 MaxItemCount = -1, 
 MaxDegreeOfParallelism = -1, 
 EnableCrossPartitionQuery = true 
 }).AsDocumentQuery();
FeedResponse<dynamic> result = await query.ExecuteNextAsync();

// Returns metrics by partition key range Id 
IReadOnlyDictionary<string, QueryMetrics> metrics = result.QueryMetrics;
```

*QueryMetrics* fournit des informations détaillées sur la durée d’exécution de chaque composant de la requête. Le plus souvent, la lenteur d’exécution des requêtes est due aux recherches qui sont effectuées lorsqu’une requête n’a pas pu utiliser les index. Ce problème peut être résolu avec une meilleure condition de filtre.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous savez comment surveiller et déboguer les problèmes à l’aide des métriques fournies dans le portail Azure, découvrez comment améliorer les performances des bases de données en lisant les articles suivants :

* [Test des performances et de la mise à l’échelle avec Azure Cosmos DB](performance-testing.md)
* [Conseils sur les performances pour Azure Cosmos DB](performance-tips.md)
