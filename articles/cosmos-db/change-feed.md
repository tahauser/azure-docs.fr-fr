---
title: Utilisation du support de flux de modification dans Azure Cosmos DB | Microsoft Docs
description: "Utilisez le support de flux de modification d’Azure Cosmos DB pour suivre les modifications dans les documents et effectuer des opérations de traitement basées sur les événements tels que des déclencheurs et la mise à jour des systèmes de cache et d’analyse."
keywords: change feed
services: cosmos-db
author: rafats
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: 2d7798db-857f-431a-b10f-3ccbc7d93b50
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: 
ms.topic: article
ms.date: 01/29/2018
ms.author: rafats
ms.openlocfilehash: e0940ab11f8840ffa2dbdbfd739340f8af5caf51
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/24/2018
---
# <a name="working-with-the-change-feed-support-in-azure-cosmos-db"></a>Utilisation du support de flux de modification dans Azure Cosmos DB

[Azure Cosmos DB](../cosmos-db/introduction.md) est une base de données répliquée mondialement à la fois rapide et flexible, idéale pour les applications d’IoT, de jeux, de vente au détail et de journalisation des compteurs. Ces applications intègrent souvent un modèle de conception qui consiste à utiliser des modifications de données pour déclencher des actions supplémentaires. Voici quelques exemples d’actions supplémentaires : 

* Déclenchement d’une notification ou d’un appel à une API lorsqu’un document est inséré ou modifié ;
* Traitement de flux pour l’IoT ou exécution d’analyses ;
* Déplacement de données supplémentaire via une synchronisation avec un cache, un moteur de recherche ou un entrepôt de données, ou archivage de données dans un stockage à froid.

Le **support de flux de modification** d’Azure Cosmos DB vous permet de créer des solutions efficaces et évolutives pour chacun de ces modèles, comme illustré dans l’image suivante :

![Utilisation du flux de modification d’Azure Cosmos DB pour alimenter les analyses en temps réel et les scénarios de calcul pilotés par les événements](./media/change-feed/changefeedoverview.png)

> [!NOTE]
> Le support de flux de modification est fourni pour tous les conteneurs et modèles de données dans Azure Cosmos DB. Toutefois, le flux de modification est lu à l’aide du client SQL et sérialise les éléments au format JSON. En raison du formatage JSON, les clients MongoDB seront confrontés à une incompatibilité entre les documents au format BSON et le flux de modification au format JSON. 

## <a name="how-does-change-feed-work"></a>Comment fonctionne le flux de modification ?

Le support de flux de modification dans Azure Cosmos DB consiste à identifier les modifications apportées à une collection Azure Cosmos DB. Il renvoie ensuite la liste chronologique de documents qui ont été modifiés, dans l’ordre dans lequel ils ont été modifiés. Les modifications sont conservées et peuvent être traitées de manière asynchrone et incrémentielle, puis réparties sur un ou plusieurs consommateurs pour un traitement en parallèle. 

Vous pouvez lire le flux de modification de trois manières différentes, comme expliqué plus loin dans cet article :

1.  [Utilisation d’Azure Functions](#azure-functions)
2.  [Utilisation du Kit de développement logiciel (SDK) Azure Cosmos DB](#rest-apis)
3.  [Utilisation de la bibliothèque du processus de flux de modification Azure Cosmos DB](#change-feed-processor)

Le flux de modification est disponible pour chaque plage de clés de partition dans la collection de documents et peut ainsi être distribué vers un ou plusieurs consommateurs pour un traitement en parallèle, comme indiqué dans l’image suivante.

![Traitement distribué du flux de modification d’Azure Cosmos DB](./media/change-feed/changefeedvisual.png)

Informations supplémentaires :
* Le flux de modification est activé par défaut pour tous les comptes.
* Vous pouvez utiliser votre [débit configuré](request-units.md) dans votre région d’écriture ou toute [région de lecture](distribute-data-globally.md) pour lire à partir du flux de modification comme toute autre opération effectuée à partir d’Azure Cosmos DB.
* Le flux de modification inclut les insertions et les opérations de mise à jour apportées aux documents de la collection. Vous pouvez capturer des suppressions en définissant un indicateur de « suppression réversible » dans vos documents à la place des suppressions. Vous pouvez également définir un délai d’expiration limité pour vos documents via la [fonctionnalité de durée de vie (TTL)](time-to-live.md), par exemple 24 heures, et utiliser la valeur de cette propriété pour capturer les suppressions. Avec cette solution, vous devez traiter les modifications dans un intervalle de temps inférieur à la période d’expiration de durée de vie.
* Chaque modification apportée à un document n’apparaît qu’une seule fois dans le flux des modifications. En outre, les clients gèrent leur logique de points de contrôle. La bibliothèque du processeur de flux des modifications fournit des points de contrôle automatiques, ainsi qu’une sémantique de type « au moins une fois ».
* Seule la dernière modification d’un document donné est incluse dans le journal des modifications. Les modifications intermédiaires peuvent ne pas être disponibles.
* Le flux de modification est trié par ordre de modification au sein de chaque valeur de clé de partition. Il n’existe aucun ordre garanti entre les valeurs de clé de partition.
* Les modifications peuvent être synchronisées à partir de n’importe quel moment donné ; autrement dit, il n’existe aucune période de rétention de données fixes pendant laquelle les modifications sont disponibles.
* Les modifications sont disponibles dans des blocs de plages de clés de partition. Cette fonctionnalité permet le traitement en parallèle de modifications de grandes collections par plusieurs consommateurs/serveurs.
* Les applications peuvent demander plusieurs flux de modification simultanément sur la même collection.
* ChangeFeedOptions.StartTime peut servir à fournir un point de départ initial, par exemple, pour trouver le jeton de continuation correspondant à une heure donnée. S’il est spécifié, ContinuationToke l’emporte sur les valeurs StartTime et StartFromBeginning. La précision de ChangeFeedOptions.StartTime est ’environ 5 secondes. 

## <a name="use-cases-and-scenarios"></a>Cas d'utilisation et scénarios

Le flux de modification permet un traitement efficace des jeux de données importants avec un volume d’écritures élevé et offre une alternative à l’interrogation des jeux de données pour identifier les changements apportés. 

Par exemple, avec un flux de modification, vous pouvez effectuer efficacement les tâches suivantes :

* Mettre à jour un cache, l’index de recherche ou un entrepôt de données avec les données stockées dans Azure Cosmos DB.
* Implémenter la hiérarchisation et l’archivage des données de niveau application, autrement dit, stocker les « données à chaud » dans Azure Cosmos DB et archiver les « données à froid » dans [Stockage Blob Azure](../storage/common/storage-introduction.md) ou [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md).
* Effectuer des migrations sans aucun temps d’arrêt vers un autre compte Azure Cosmos DB avec un schéma de partitionnement différent.
* Implémenter des [pipelines lambda sur Azure](https://blogs.technet.microsoft.com/msuspartner/2016/01/27/azure-partner-community-big-data-advanced-analytics-and-lambda-architecture/) avec Azure Cosmos DB. Azure Cosmos DB fournit une solution de base de données évolutive qui peut gérer l’ingestion et l’interrogation, et implémenter des architectures lambda avec un faible coût total de possession. 
* Recevoir et stocker des données d’événement à partir d’appareils, de capteurs, de l’infrastructure et des applications, et traiter ces événements en temps réel avec [Azure Stream Analytics](../stream-analytics/stream-analytics-documentdb-output.md), [Apache Storm](../hdinsight/storm/apache-storm-overview.md) ou [Apache Spark](../hdinsight/spark/apache-spark-overview.md). 

L’image suivante montre de quelle manière des pipelines lambda utilisés à la fois pour ingérer et interroger des données à l’aide d’Azure Cosmos DB peuvent utiliser le support de flux de modification : 

![Pipeline lambda Azure Cosmos DB pour l’ingestion et l’interrogation](./media/change-feed/lambda.png)

En outre, dans les applications web et mobiles [sans serveur](http://azure.com/serverless), vous pouvez suivre les événements tels que les modifications apportées au profil, aux préférences ou à l’emplacement de votre client pour déclencher certaines actions, comme l’envoi de notifications Push à leurs appareils avec [Azure Functions](#azure-functions). Si vous utilisez Azure Cosmos DB pour créer un jeu, vous pouvez, par exemple, utiliser le flux de modification pour implémenter des classements en temps réel basés sur des scores de jeux terminés.

<a id="azure-functions"></a>
## <a name="using-azure-functions"></a>Utilisation d’Azure Functions 

Si vous utilisez Azure Functions, la façon la plus simple pour vous connecter à un flux de modification Azure Cosmos DB consiste à ajouter un déclencheur Azure Cosmos DB à votre application Azure Functions. Lorsque vous créez un déclencheur Azure Cosmos DB dans une application Azure Functions, vous sélectionnez la collection Azure Cosmos DB à laquelle vous souhaitez vous connecter, et la fonction se déclenche chaque fois qu’une modification est apportée à la collection sélectionnée. 

Les déclencheurs peuvent être créés via le portail Azure Functions, via le portail Azure Cosmos DB ou par programmation. Pour en savoir plus, consultez l’article [Azure Cosmos DB : traitement de base de données sans serveur à l’aide d’Azure Functions](serverless-computing-database.md).

<a id="rest-apis"></a>
## <a name="using-the-sdk"></a>Utilisation du kit de développement logiciel

Le [SDK SQL](sql-api-sdk-dotnet.md) pour Azure Cosmos DB comprend toutes les fonctionnalités nécessaires pour lire et gérer un flux de modification. De telles fonctionnalités impliquent également un certain nombre de responsabilités. Si vous voulez gérer les points de contrôle et les numéros de séquence des documents, et bénéficier d’un contrôle granulaire sur les clés de partition, alors utiliser le Kit de développement logiciel (SDK) peut être une bonne alternative.

Cette section vous explique comment utiliser le SDK SQL pour exploiter les flux de modification.

1. Commencez par consulter les ressources suivantes disponibles dans appconfig. Des instructions sur la récupération de la clé d’autorisation et du point de terminaison sont disponibles dans [Mise à jour de votre chaîne de connexion](create-sql-api-dotnet.md#update-your-connection-string).

    ``` csharp
    DocumentClient client;
    string DatabaseName = ConfigurationManager.AppSettings["database"];
    string CollectionName = ConfigurationManager.AppSettings["collection"];
    string endpointUrl = ConfigurationManager.AppSettings["endpoint"];
    string authorizationKey = ConfigurationManager.AppSettings["authKey"];
    ```

2. Créez le client comme suit :

    ```csharp
    using (client = new DocumentClient(new Uri(endpointUrl), authorizationKey,
    new ConnectionPolicy { ConnectionMode = ConnectionMode.Direct, ConnectionProtocol = Protocol.Tcp }))
    {
    }
    ```

3. Récupérez les plages de clés de partition :

    ```csharp
    FeedResponse pkRangesResponse = await client.ReadPartitionKeyRangeFeedAsync(
        collectionUri,
        new FeedOptions
            {RequestContinuation = pkRangesResponseContinuation });
     
    partitionKeyRanges.AddRange(pkRangesResponse);
    pkRangesResponseContinuation = pkRangesResponse.ResponseContinuation;
    ```

4. Appelez ExecuteNextAsync pour chaque plage de clés de partition :

    ```csharp
    foreach (PartitionKeyRange pkRange in partitionKeyRanges){
        string continuation = null;
        checkpoints.TryGetValue(pkRange.Id, out continuation);
        IDocumentQuery<Document> query = client.CreateDocumentChangeFeedQuery(
            collectionUri,
            new ChangeFeedOptions
            {
                PartitionKeyRangeId = pkRange.Id,
                StartFromBeginning = true,
                RequestContinuation = continuation,
                MaxItemCount = -1,
                // Set reading time: only show change feed results modified since StartTime
                StartTime = DateTime.Now - TimeSpan.FromSeconds(30)
            });
        while (query.HasMoreResults)
            {
                FeedResponse<dynamic> readChangesResponse = query.ExecuteNextAsync<dynamic>().Result;
    
                foreach (dynamic changedDocument in readChangesResponse)
                    {
                         Console.WriteLine("document: {0}", changedDocument);
                    }
                checkpoints[pkRange.Id] = readChangesResponse.ResponseContinuation;
            }
    }
    ```

Si vous disposez de plusieurs lecteurs, vous pouvez utiliser **ChangeFeedOptions** pour répartir la charge de lecture sur plusieurs threads ou clients.

Ces quelques lignes de code suffisent pour commencer à lire le flux de modification. Vous pouvez obtenir le code complet utilisé dans cet article à partir du [référentiel GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/ChangeFeedProcessor).

Dans le code de l’étape 4 ci-dessus, la valeur **ResponseContinuation** de la dernière ligne comprend le dernier numéro de séquence logique du document, que vous utiliserez la prochaine fois que vous lirez de nouveaux documents après ce numéro de séquence. L’élément **StartTime** de la méthode **ChangeFeedOption** vous permet d’élargir le champ de récupération des documents. Ainsi, si la valeur **ResponseContinuation** est nulle mais que la valeur **StartTime** renvoie à une date antérieure, vous obtiendrez tous les documents modifiés depuis l’heure correspondant à la valeur **StartTime**. Par contre, si la valeur **ResponseContinuation** n’est pas nulle, le système collectera tous les documents modifiés depuis ce numéro de séquence logique.

Ainsi, votre tableau de points de contrôle conserve uniquement le numéro de séquence logique pour chaque partition. Toutefois, si vous ne souhaitez pas gérer les partitions, les points de contrôle, le numéro de séquence logique, l’heure de début, etc., l’option la plus simple consiste à utiliser la bibliothèque du processeur de flux de modification.

<a id="change-feed-processor"></a>
## <a name="using-the-change-feed-processor-library"></a>Utilisation de la bibliothèque du processeur de flux de modification 

La [bibliothèque du processeur de flux de modification Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/sql-api-sdk-dotnet-changefeed) vous permet de répartir facilement le traitement des événements sur plusieurs consommateurs d’événements. Cette bibliothèque simplifie la lecture des modifications sur plusieurs partitions et threads exécutés en parallèle.

Le principal avantage de la bibliothèque du processeur de flux de modification est que vous n’êtes pas obligé de gérer chaque partition et jeton de liaison, et que vous n’avez pas à interroger chaque collection manuellement.

La bibliothèque du processeur de flux de modification simplifie la lecture des modifications sur plusieurs partitions et threads exécutés en parallèle.  Elle gère automatiquement la lecture des modifications sur les différentes partitions à l’aide d’un mécanisme de bail. Comme vous pouvez le voir dans l’image suivante, si vous démarrez deux clients qui utilisent la bibliothèque du processeur de flux de modification, ces deux clients se partagent la charge de travail. Plus vous ajoutez de clients, plus la charge de travail est répartie entre ces clients.

![Traitement distribué du flux de modification d’Azure Cosmos DB](./media/change-feed/change-feed-output.png)

Le client de gauche a été démarré en premier et a commencé à analyser l’ensemble des partitions. Puis, lors du démarrage du deuxième client, le premier client lui a transmis certains des baux. Comme vous pouvez le voir, cela permet de distribuer facilement la charge de travail entre différents ordinateurs et clients.

Notez que si vous avez deux fonctions Azure sans serveur qui analysent la même collection et utilisent le même bail, les deux fonctions peuvent obtenir des documents différents en fonction de la manière dont la bibliothèque du processeur décide de traiter les partitions.

<a id="understand-cf"></a>
### <a name="understanding-the-change-feed-processor-library"></a>Présentation de la bibliothèque du processeur de flux de modification

L’implémentation du processeur de flux de modification fait appel à quatre composants principaux : la collection analysée, la collection de baux, l’hôte de processeur et les consommateurs. 

> [!WARNING]
> La création d’une collection a des conséquences d’un point de vue tarifaire. En effet, vous réservez une part du débit pour que l’application puisse communiquer avec Azure Cosmos DB. Pour plus d’informations, consultez la [page de tarification](https://azure.microsoft.com/pricing/details/cosmos-db/).
> 
> 

**Collecte analysé :** la collection analysée est constituée des données à partir desquelles le flux de modification est généré. Toutes les insertions et les modifications apportées à la collection analysée sont répercutées dans le flux de modification de la collection. 

**Collection de baux :** la collection de baux coordonne le traitement du flux de modification parmi les threads de travail. Une collection distincte est utilisée pour stocker les baux avec un bail par partition. Il est préférable de stocker cette collection de baux sur un autre compte, avec une zone d’écriture plus proche de l’endroit où le processeur de flux de modification s’exécute. Un objet de bail contient les attributs suivants : 
* Propriétaire : spécifie l’hôte détenteur du bail.
* Continuation : spécifie la position (jeton de continuation) d’une partition particulière dans le flux de modification.
* Horodatage : heure de dernière mise à jour du bail. Vous pouvez utiliser l’horodatage pour vérifier si le bail est considéré comme ayant expiré. 

**Hôte de processeur :** chaque hôte détermine le nombre de partitions à traiter en fonction de la quantité d’autres instances d’hôtes ayant des baux actifs. 
1.  Quand un hôte démarre, il acquiert des baux pour équilibrer la charge de travail parmi tous les hôtes. Un hôte renouvelle périodiquement les baux afin que ceux-ci restent actifs. 
2.  Un hôte crée un point de contrôle pour le dernier jeton de continuation de son bail pour chaque lecture. Pour garantir la sécurité de l’accès concurrentiel, un hôte vérifie l’ETag pour chaque mise à jour de bail. D’autres stratégies de point de contrôle sont également prises en charge.  
3.  En cas d’arrêt, un hôte libère tous les baux mais conserve les informations de continuation, afin de pouvoir reprendre ultérieurement la lecture à partir du point de contrôle stocké. 

À ce stade, le nombre d’hôtes ne peut pas être supérieur au nombre de partitions (baux).

**Consommateurs :** les consommateurs, ou Workers, sont des threads qui exécutent le traitement du flux de modification initié par chaque hôte. Chaque hôte de processeur peut avoir plusieurs consommateurs. Chaque consommateur lit le flux de modification à partir de la partition à laquelle il est assigné, et il informe son hôte des modifications et des baux expirés.

Pour mieux comprendre comment ces quatre éléments du processeur de flux de modification interagissent, examinons un exemple à l’aide du schéma suivant. La collection analysée stocke des documents et utilise « city » comme clé de partition. Nous constatons que la partition bleue contient des documents avec le champ « city » de « A-E », et ainsi de suite. Il existe deux hôtes, chacun avec deux consommateurs lisant à partir des quatre partitions en parallèle. Les flèches indiquent les consommateurs lisant à partir d’un point spécifique dans le flux de modification. Dans la première partition, le bleu foncé représente les modifications non lues, tandis que le bleu clair représente les modifications déjà lues sur le flux de modification. Les hôtes utilisent la collection de baux pour stocker une valeur de « continuation » afin d’assurer le suivi de la position de lecture actuelle pour chaque consommateur. 

![Utilisation de l’hôte du processus de flux de modification Azure Cosmos DB](./media/change-feed/changefeedprocessornew.png)

### <a name="working-with-the-change-feed-processor-library"></a>Utilisation de la bibliothèque du processeur de flux de modification

Avant d’installer le package NuGet du processeur de flux de modification, vous devez d’abord installer : 

* Microsoft.Azure.DocumentDB, version 1.13.1 ou ultérieure 
* Newtonsoft.Json, version 9.0.1 ou ultérieure

Installez ensuite le [package NuGet Microsoft.Azure.DocumentDB.ChangeFeedProcessor](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/) et ajoutez-le en tant que référence.

Pour implémenter la bibliothèque du processeur de flux de modification, procédez comme suit :

1. Implémentez un objet **DocumentFeedObserver** qui implémente un objet **IChangeFeedObserver**.

2. Implémentez un objet **DocumentFeedObserverFactory** qui implémente un objet **IChangeFeedObserverFactory**.

3. Dans la méthode **CreateObserver** de l’objet **DocumentFeedObserverFactory**, instanciez l’objet **ChangeFeedObserver** que vous avez créé à l’étape 1 et renvoyez-le.

    ```
    public IChangeFeedObserver CreateObserver()
    {
              DocumentFeedObserver newObserver = new DocumentFeedObserver(this.client, this.collectionInfo);
              return newObserver;
    }
    ```

4. Instanciez l’objet **DocumentObserverFactory**.

5. Instanciez un objet **ChangeFeedEventHost** :

    ```csharp
    ChangeFeedEventHost host = new ChangeFeedEventHost(
                     hostName,
                     documentCollectionLocation,
                     leaseCollectionLocation,
                     feedOptions,
                     feedHostOptions);
    ```

6. Enregistrez l’objet **DocumentFeedObserverFactory** auprès de l’hôte.

Le code pour les étapes 4 à 6 est le suivant : 

```
ChangeFeedOptions feedOptions = new ChangeFeedOptions();
feedOptions.StartFromBeginning = true;

ChangeFeedHostOptions feedHostOptions = new ChangeFeedHostOptions();
 
// Customizing lease renewal interval to 15 seconds.
// Can customize LeaseRenewInterval, LeaseAcquireInterval, LeaseExpirationInterval, FeedPollDelay
feedHostOptions.LeaseRenewInterval = TimeSpan.FromSeconds(15);
 
using (DocumentClient destClient = new DocumentClient(destCollInfo.Uri, destCollInfo.MasterKey))
{
        DocumentFeedObserverFactory docObserverFactory = new DocumentFeedObserverFactory(destClient, destCollInfo);
        ChangeFeedEventHost host = new ChangeFeedEventHost(hostName, documentCollectionLocation, leaseCollectionLocation, feedOptions, feedHostOptions);
        await host.RegisterObserverFactoryAsync(docObserverFactory);
        await host.UnregisterObserversAsync();
}
```

Et voilà ! Après ces quelques étapes, des documents seront progressivement collectés au sein de la méthode **DocumentFeedObserver ProcessChangesAsync**.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’utilisation d’Azure Cosmos DB avec Azure Functions, consultez l’article [Azure Cosmos DB : traitement de base de données sans serveur à l’aide d’Azure Functions](serverless-computing-database.md).

Pour plus d’informations sur l’utilisation de la bibliothèque du processeur de flux de modification, utilisez les ressources suivantes :

* [Page d’informations](sql-api-sdk-dotnet-changefeed.md) 
* [Package NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)
* [Exemple de code illustrant les étapes 1 à 6 ci-dessus](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/ChangeFeedProcessor)
* [Exemples supplémentaires sur GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/ChangeFeedProcessor)

Pour plus d’informations sur l’utilisation du flux de modification avec le Kit de développement logiciel (SDK), utilisez les ressources suivantes :

* [Page d’informations sur le Kit de développement logiciel (SDK)](sql-api-sdk-dotnet.md)
