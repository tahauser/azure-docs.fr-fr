---
title: "Partitionnement et mise à l’échelle horizontale dans Azure Cosmos DB | Microsoft Docs"
description: "Découvrez comment le partitionnement fonctionne dans Azure Cosmos DB, comment configurer le partitionnement et les clés de partition, et comment choisir la clé de partition appropriée pour votre application."
services: cosmos-db
author: arramac
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: cac9a8cd-b5a3-4827-8505-d40bb61b2416
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2018
ms.author: arramac
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0032a00883cedfe754e14293dc13a1009f6dd3a0
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/05/2018
---
# <a name="partition-and-scale-in-azure-cosmos-db"></a>Partitionner et mettre à l’échelle dans Azure Cosmos DB

[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) est un service de base de données multimodèle distribué à l’échelle mondiale, qui a été conçu pour vous permettre d’accéder à des performances élevées et prévisibles. Il se met à l’échelle en toute transparence à mesure que votre application évolue. Cet article offre une vue d’ensemble du fonctionnement du partitionnement pour toutes les modèles de données dans Azure Cosmos DB. Il vous explique aussi comment configurer des conteneurs Azure Cosmos DB pour mettre efficacement vos applications à l’échelle.

La question du partitionnement et des clés de partition est abordée dans cette vidéo Azure Friday avec Scott Hanselman et Shireesh Thota, responsable principal de l’ingénierie Azure Cosmos DB :

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Azure-DocumentDB-Elastic-Scale-Partitioning/player]
> 

## <a name="partitioning-in-azure-cosmos-db"></a>Partitionnement dans Azure Cosmos DB
Dans Azure Cosmos DB, vous pouvez stocker et interroger des données sans schéma avec des délais de réponse de l’ordre des millisecondes à n’importe quelle échelle. Azure Cosmos DB propose des conteneurs pour le stockage des données, appelés *collections* (pour les documents), *graphes* ou *tables*. 

Les conteneurs sont des ressources logiques. Ils peuvent s’étendre sur plusieurs partitions physiques ou serveurs. Le nombre de partitions est déterminé par Azure Cosmos DB en fonction de la taille de stockage et du débit approvisionné du conteneur. 

Une partition physique est une quantité fixe de stockage SSD réservé. Chaque partition physique est répliquée à des fins de haute disponibilité. Une ou plusieurs partitions physiques constituent un conteneur. La gestion des partitions physiques est entièrement assurée par Azure Cosmos DB, et vous n’avez pas à écrire de code complexe ni à gérer vos partitions. Les conteneurs Azure Cosmos DB sont illimités en termes de débit et de stockage. 

Une partition logique est une partition dans une partition physique qui stocke toutes les données associées à une valeur de clé de partition unique. Une partition logique a un maximum de 10 Go. Dans le diagramme suivant, un conteneur unique comporte trois partitions logiques. Chaque partition logique stocke les données pour une clé de partition, LAX, AMS et MEL respectivement. Aucune des partitions logiques LAX, AMS et MEL ne peut croître au-delà de la limite maximale de partition logique de 10 Go. 

![Partitionnement des ressources](./media/introduction/azure-cosmos-db-partitioning.png) 

Quand une collection satisfait aux [prérequis pour le partitionnement](#prerequisites), l’acte de partitionnement est transparent pour votre application. Azure Cosmos DB prend en charge les lectures/écritures rapides, les requêtes, la logique transactionnelle, les niveaux de cohérence et le contrôle d’accès précis d’une ressource de conteneur unique à l’aide de méthodes/API. Le service gère la distribution des données entre les partitions physiques et logiques et le routage des demandes de requête vers la partition appropriée. 

## <a name="how-does-partitioning-work"></a>Fonctionnement du partitionnement

Comment le partitionnement fonctionne-t-il ? Chaque élément doit avoir une clé de partition et une clé de ligne, qui l’identifient de façon. Votre clé de partition agit comme une partition logique pour vos données et fournit à Azure Cosmos DB une frontière naturelle pour la distribution de données entre plusieurs partitions physiques. Gardez à l’esprit que les données pour une partition logique unique doivent résider dans une seule partition physique, mais que la gestion des partitions physiques est gérée par Azure Cosmos DB. 

En bref, voici comment le partitionnement fonctionne dans Azure Cosmos DB :

* Vous provisionnez un conteneur Azure Cosmos DB avec un débit de **T** requêtes par seconde.
* En arrière-plan, Azure Cosmos DB provisionne les partitions nécessaires pour servir **T** requêtes par seconde. Si la valeur de **T** est supérieure au débit maximal par partition **t**, Azure Cosmos DB provisionne **N = T/t** partitions.
* Azure Cosmos DB alloue l’espace des hachages de clé de partition uniformément entre les **N** partitions. Ainsi, chaque partition (physique) héberge **1/N** valeurs de clé de partition (partitions logiques).
* Quand une partition physique **p** atteint sa limite de stockage, Azure Cosmos DB fractionne **p** en deux partitions nouvelles : **p1** et **p2**. Des valeurs correspondant à environ la moitié des clés sont distribuées à chacune des partitions. Cette opération de division est invisible pour votre application. Si une partition physique atteint sa limite de stockage et que toutes les données dans la partition physique appartiennent à la même clé de partition logique, l’opération de fractionnement n’a pas lieu. En effet, comme toutes les données d’une clé de partition logique unique doivent résider dans la même partition physique, celle-ci ne peut pas être fractionnée en p1 et p2. Dans ce cas, une stratégie de clé de partition différente doit être employée.
* Quand vous provisionnez un débit supérieur à **t*N**, Azure Cosmos DB fractionne une ou plusieurs de vos partitions pour supporter le débit plus élevé.

La sémantique pour les clés de partition est légèrement différente pour correspondre à celle de chaque API, comme indiqué dans le tableau suivant :

| API | Clé de partition | Clé de ligne |
| --- | --- | --- |
| Azure Cosmos DB | Chemin de clé de partition personnalisée | `id` fixe | 
| MongoDB | Clé partagée personnalisée  | `_id` fixe | 
| Graph | Propriété de clé de partition personnalisée | `id` fixe | 
| Table | `PartitionKey` fixe | `RowKey` fixe | 

Azure Cosmos DB utilise le partitionnement basé sur le hachage. Quand vous écrivez un élément, Azure Cosmos DB hache la valeur de clé de partition et utilise le résultat haché pour déterminer dans quelle partition stocker l’élément. Azure Cosmos DB stocke tous les éléments avec la même clé de partition dans la même partition physique. Le choix de la clé de partition est une décision importante que vous devrez prendre au moment de la conception. Vous devez choisir un nom de propriété qui possède une large plage de valeurs et des modèles d’accès uniformes. Si une partition physique atteint sa limite de stockage et que la même clé de partition se trouve sur toutes les données dans la partition, Azure Cosmos DB retourne une erreur indiquant que la clé de partition a atteint la taille maximale de 10 Go, et la partition n’est pas fractionnée ; ainsi, choisir une clé de partition est une décision très importante.

> [!NOTE]
> Il est recommandé d’avoir une clé de partition associée à un grand nombre de valeurs distinctes (de plusieurs centaines à plusieurs milliers, au minimum).
>

Les conteneurs Azure Cosmos DB peuvent être créés *fixes* ou *illimités* dans le portail Azure. Les conteneurs de taille fixe ont une limite maximale de 10 Go et de 10 000 RU/s de débit. Pour créer un conteneur dit illimité, vous devez spécifier un débit minimal de 1 000 RU/s et une clé de partition.

Il est judicieux de vérifier la façon dont vos données sont réparties dans les partitions. Pour vérifier cette répartition dans le portail, connectez-vous à votre compte Azure Cosmos DB et cliquez sur **Mesures** dans la section **Surveillance** ; ensuite, dans le volet de droite, cliquez sur l’onglet **Stockage** pour déterminer le mode de répartition des données dans une autre partition physique.

![Partitionnement des ressources](./media/partition-data/partitionkey-example.png)

L’image de gauche indique le résultat d’une clé de partition incorrecte et l’image de droite, le résultat d’une clé de partition correcte. Dans l’image de gauche, vous pouvez voir que les données ne sont pas réparties uniformément sur les partitions. Vous devez vous efforcer de distribuer vos données de sorte que votre graphique soit similaire à l’image de droite.

<a name="prerequisites"></a>
## <a name="prerequisites-for-partitioning"></a>Prérequis pour le partitionnement

Pour que les partitions physiques se fractionnent automatiquement en **p1** et **p2** comme décrit dans [Fonctionnement du partitionnement](#how-does-partitioning-work), vous devez créer le conteneur avec un débit minimal de 1 000 RU/s et fournir une clé de partition. Quand vous créez un conteneur dans le portail Azure, sélectionnez l’option de capacité de stockage **Illimité** pour tirer parti du partitionnement et de la mise à l’échelle automatique. 

Si vous avez créé un conteneur dans le portail Azure ou par programmation, que le débit initial minimal était de 1 000 RU/s et que vos données incluent une clé de partition, vous pouvez tirer parti du partitionnement sans changer votre conteneur : vous pouvez obtenir des conteneurs de taille **fixe**, à condition que le conteneur initial ait été créé avec un débit minimal de 1 000 RU/s et qu’une clé de partition soit présente dans les données.

Si vous avez créé un conteneur de taille **fixe** sans clé de partition ou créé un conteneur de taille **fixe** avec un débit inférieur à 1 000 RU/s, le conteneur ne peut pas se fractionner automatiquement comme décrit dans cet article. Pour migrer des données à partir d’un conteneur comme celui-ci vers un conteneur illimité (doté d’un débit minimal de 1 000 RU/s et d’une clé de partition), vous devez utiliser [l’outil de migration de données](import-data.md) ou la [bibliothèque de flux de modification](change-feed.md) pour migrer les modifications. 

## <a name="partitioning-and-provisioned-throughput"></a>Partitionnement et débit approvisionné
Azure Cosmos DB est conçu pour offrir des performances prévisibles. Quand vous créez un conteneur, vous réservez un débit en termes *[d’unités de requête](request-units.md) (RU) par seconde*. Des frais d’UR proportionnels à la quantité de ressources système, comme le processeur, la mémoire et les E/S consommées par l’opération, sont imputés à chaque requête. La lecture d’un document de 1 Ko avec une cohérence de session consomme 1 RU. Une lecture correspond à 1 RU, quel que soit le nombre d’éléments stockés ou le nombre de demandes simultanées en cours d’exécution. Les éléments plus volumineux exigent des RU supérieures en rapport avec la taille. Si vous connaissez la taille de vos entités et le nombre de lectures nécessaires à prendre en charge pour votre application, vous pouvez approvisionner la quantité exacte de débit requis pour les besoins en lecture de votre application. 

> [!NOTE]
> Afin d’optimiser le débit total du conteneur, vous devez choisir une clé de partition qui vous permet de répartir uniformément les demandes entre certaines valeurs de clé de partition distinctes.
> 
> 

<a name="designing-for-partitioning"></a>
## <a name="work-with-the-azure-cosmos-db-apis"></a>Utilisation des API Azure Cosmos DB
Vous pouvez utiliser le portail Azure ou l’interface CLI Azure pour créer des conteneurs et les faire évoluer à tout moment. Cette section montre comment créer des conteneurs et spécifier la définition de clé de partition et le débit dans chacune des API prises en charge.

### <a name="azure-cosmos-db-api"></a>API Azure Cosmos DB
L’exemple suivant montre comment créer un conteneur (collection) à l’aide de l’API Azure Cosmos DB. 

```csharp
DocumentClient client = new DocumentClient(new Uri(endpoint), authKey);
await client.CreateDatabaseAsync(new Database { Id = "db" });

DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "coll";
myCollection.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("db"),
    myCollection,
    new RequestOptions { OfferThroughput = 20000 });
```

Vous pouvez lire un élément (document) à l’aide de la méthode `GET` dans l’API REST ou à l’aide de `ReadDocumentAsync` dans l’un des kits SDK.

```csharp
// Read document. Needs the partition key and the ID to be specified
DeviceReading document = await client.ReadDocumentAsync<DeviceReading>(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
  new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });
```

### <a name="mongodb-api"></a>API MongoDB
Avec l’API MongoDB, vous pouvez créer une collection partitionnée MongoDB via l’outil, le pilote ou le SDK de votre choix. Dans cet exemple, nous utilisons l’interpréteur de commandes Mongo pour la création de la collection.

Dans l’interpréteur de commandes Mongo :

```
db.runCommand( { shardCollection: "admin.people", key: { region: "hashed" } } )
```
    
Résultats :

```JSON
{
    "_t" : "ShardCollectionResponse",
    "ok" : 1,
    "collectionsharded" : "admin.people"
}
```

### <a name="table-api"></a>API de table

Pour créer une table à l’aide de l’API de table Azure Cosmos DB, utilisez la méthode CreateIfNotExists. 

```csharp
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

CloudTable table = tableClient.GetTableReference("people");
table.CreateIfNotExists(throughput: 800);
```

Le débit est défini en tant qu’argument de CreateIfNotExists.

La clé de partition est implicitement créée en tant que valeur `PartitionKey`. 

Vous pouvez récupérer une entité unique à l’aide de l’extrait de code suivant :

```csharp
// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the retrieve operation.
TableResult retrievedResult = table.Execute(retrieveOperation);
```
Pour plus d’informations, consultez [Développer avec l’API Table](tutorial-develop-table-dotnet.md).

### <a name="graph-api"></a>API Graph

Avec l’API Graph, vous devez utiliser le portail Azure ou l’interface Azure CLI pour créer des conteneurs. Azure Cosmos DB étant multimodèle, vous pouvez aussi utiliser l’un des autres modèles pour créer et mettre à l’échelle votre conteneur de graphes.

Vous pouvez lire un sommet ou une arête à l’aide de la clé de partition et l’ID dans Gremlin. Par exemple, pour un graphe ayant la région (« USA ») comme clé de partition et « Seattle » comme clé de ligne, vous pouvez trouver un vertex en utilisant la syntaxe suivante :

```
g.V(['USA', 'Seattle'])
```

Vous pouvez faire référence à une arête en utilisant la clé de partition et la clé de ligne.

```
g.E(['USA', 'I5'])
```

Pour plus d’informations, consultez [Prise en charge de Gremlin pour Azure Cosmos DB](gremlin-support.md).


<a name="designing-for-partitioning"></a>
## <a name="design-for-partitioning"></a>Conception axée sur le partitionnement
Pour mettre à l’échelle efficacement avec Azure Cosmos DB, vous devez choisir une bonne clé de partition lorsque vous créez votre conteneur. Il y a principalement deux éléments à prendre en considération quand il s’agit de choisir une clé de partition :

* **Limite des requêtes et des transactions**. Votre choix de clé de partition doit équilibrer la nécessité d’utiliser des transactions et la nécessité de répartir les entités sur plusieurs clés de partitions pour garantir une solution évolutive. D’un côté, vous pouvez définir la même clé de partition pour tous vos éléments, mais cette solution peut limiter la scalabilité de votre solution. D’un autre côté, vous pouvez affecter une clé de partition unique à chaque élément. Si ce choix s’avère hautement scalable, il vous empêche en revanche d’utiliser des transactions entre documents via des procédures stockées et des déclencheurs. Une clé de partition idéale vous permet d’utiliser des requêtes efficaces et présente une cardinalité suffisante pour garantir la scalabilité de votre solution. 
* **Absence de goulots d’étranglement au niveau des performances et du stockage**. Il est important de choisir une propriété qui permet la distribution des écritures entre plusieurs valeurs distinctes. Les demandes auprès de la même clé de partition ne peuvent pas dépasser le débit d’une partition unique et sont limitées. Il est donc important de choisir une clé de partition qui ne se traduit pas par des « zones réactives » au sein de votre application. Comme toutes les données relatives à une clé de partition unique doivent être stockées dans une partition, vous devez éviter les clés de partition qui ont des volumes de données importants pour une même valeur. 

Penchons-nous sur quelques scénarios concrets et sur les clés de partition adaptées à chaque cas :
* Si vous implémentez un backend de profils utilisateur, l’ID utilisateur constitue un bon choix pour la clé de partition.
* Si vous stockez des données IoT, comme l’état d’un appareil, l’ID de l’appareil constitue un bon choix pour la clé de partition.
* Si vous utilisez Azure Cosmos DB pour la journalisation de données de séries chronologiques, il est intéressant de choisir le nom d’hôte ou l’ID de processus pour la clé de partition.
* Si vous disposez d’une architecture multilocataire, l’ID de locataire constitue un bon choix pour la clé de partition.

Dans certains cas d’utilisation, comme l’IoT et les profils utilisateur, la clé de partition peut être identique à votre ID (clé de document). Dans d’autres cas, comme les données de séries chronologiques, la clé de partition peut être différente de l’ID.

### <a name="partitioning-and-loggingtime-series-data"></a>Partitionnement et journalisation de données de série chronologique
Azure Cosmos DB est très souvent utilisé à des fins de journalisation et de télémétrie. Il est important de choisir une bonne clé de partition, car vous pouvez être amené à lire et/ou écrire d’énormes volumes de données. Le choix dépend de vos taux de lectures et d’écritures et des types de requêtes que vous prévoyez d’exécuter. Voici quelques conseils sur la façon de choisir une clé de partition appropriée :

* Si votre cas d’utilisation implique un faible taux d’écritures qui s’accumulent sur une longue période et que vous devez interroger par plages d’horodatages et d’autres filtres, utilisez un cumul de l’horodatage. Par exemple, une bonne approche est d’utiliser la date comme clé de partition. Cette approche vous permet d’interroger toutes les données correspondant à une date à partir d’une même partition. 
* Si votre charge de travail est intense en écritures, ce qui est un cas de figure plus courant, utilisez une clé de partition qui n’est pas basée sur l’horodatage. Avec cette approche, Azure Cosmos DB peut distribuer les écritures uniformément entre différentes partitions. Dans ce cas, un nom d’hôte, un ID de processus, un ID d’activité ou une autre propriété présentant une cardinalité élevée constituent un bon choix. 
* Une troisième solution consiste à adopter une approche hybride, où vous avez plusieurs conteneurs, un pour chaque jour/mois, et où la clé de partition est une propriété granulaire comme le nom d’hôte. L’avantage de cette approche est que vous pouvez définir différents débits en fonction de la fenêtre de temps. Par exemple, le conteneur du mois en cours est approvisionné avec un débit plus élevé, car il prend en charge les lectures et les écritures. Les mois précédents sont approvisionnés avec un débit inférieur, car il ne prend en charge que les lectures.

### <a name="partitioning-and-multitenancy"></a>Partitionnement et multilocation
Si vous implémentez une application multilocataire en utilisant Azure Cosmos DB, les deux modèles les plus courants sont les suivants : une clé de partition par locataire et un conteneur par locataire. Voici les avantages et inconvénients de chaque modèle :

* **Une clé de partition par locataire**. Dans ce modèle, les locataires sont colocalisés au sein d’un même conteneur. Mais les requêtes et les insertions pour les éléments avec un seul client peuvent être exécutées sur une partition unique. Vous pouvez également implémenter une logique transactionnelle sur tous les éléments avec un client. Comme plusieurs locataires partagent un conteneur, vous pouvez vous épargner des coûts de stockage et de débit en regroupant les ressources des locataires dans un même conteneur au lieu d’approvisionner de l’espace supplémentaire pour chaque locataire. L’inconvénient est que vous ne bénéficiez pas de l’isolation des performances par locataire. Les améliorations de performances/débit s’appliquent à l’ensemble du conteneur par opposition à des améliorations ciblées pour les locataires.
* **Un conteneur par locataire**. Dans ce modèle, chaque locataire a son propre conteneur, et vous pouvez réserver des performances par locataire. Avec les nouveaux tarifs d’approvisionnement Azure Cosmos DB, ce modèle est plus rentable pour les applications multilocataires avec peu de clients.

Vous pouvez également utiliser une approche à plusieurs niveaux/combinée qui regroupe les petits clients et migre les clients plus volumineux vers leur propre conteneur.

## <a name="next-steps"></a>Étapes suivantes
Dans cet article, nous avons fourni une vue d’ensemble des concepts et des bonnes pratiques de partitionnement avec les API Azure Cosmos DB. 

* Apprenez-en davantage sur le [débit approvisionné dans Azure Cosmos DB](request-units.md).
* Renseignez-vous sur la [distribution globale dans Azure Cosmos DB](distribute-data-globally.md).



