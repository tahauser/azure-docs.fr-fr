---
title: Conseils sur les performances pour Azure Cosmos DB pour Java | Microsoft Docs
description: "Découvrez les options de configuration clientes pour améliorer les performances de base de données Azure Cosmos DB"
keywords: "comment améliorer les performances de base de données"
services: cosmos-db
author: mimig1
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: dfe8f426-3c98-4edc-8094-092d41f2795e
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/02/2018
ms.author: mimig
ms.openlocfilehash: fef5ed126575727c23cdff496c6684b9bf3192cf
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/22/2018
---
> [!div class="op_single_selector"]
> * [Java](performance-tips-java.md)
> * [.NET](performance-tips.md)
> 
> 

# <a name="performance-tips-for-azure-cosmos-db-and-java"></a>Conseils sur les performances pour Azure Cosmos DB et Java
Azure Cosmos DB est une base de données distribuée rapide et flexible qui peut être mise à l’échelle en toute transparence avec une latence et un débit garantis. Vous n’avez pas à apporter de modifications d’architecture majeures ou écrire de code complexe pour mettre à l’échelle votre base de données avec Azure Cosmos DB. Il suffit d’un simple appel d’API ou de méthode de [kit de développement logiciel (SDK)](set-throughput.md#set-throughput-java)pour effectuer une mise à l’échelle. Toutefois, étant donné qu’Azure Cosmos DB est accessible via des appels réseau, vous pouvez apporter des optimisations côté client de manière à atteindre des performances de pointe quand vous utilisez le [SDK Java SQL](documentdb-sdk-java.md).

Si vous vous demandez comment améliorer les performances de votre base de données, lisez ce qui suit :

## <a name="networking"></a>Mise en réseau
<a id="direct-connection"></a>

1. **Mode de connexion : utiliser DirectHttps**

    La façon dont un client se connecte à Azure Cosmos DB a des conséquences importantes sur les performances, notamment en termes de latence côté client. Il existe un paramètre de configuration clé disponible pour configurer la stratégie de connexion ([ConnectionPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._connection_policy)) du client : le mode de connexion [ConnectionMode](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._connection_mode).  Les deux modes de connexion disponibles sont les suivants :

   1. [Passerelle (par défaut)](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._connection_mode.gateway)
   2. [DirectHttps](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._connection_mode.directhttps)

    Le mode passerelle est pris en charge sur toutes les plateformes de SDK et est l’option configurée par défaut.  Si votre application s’exécute dans un réseau d’entreprise avec des restrictions de pare-feu strictes, la passerelle est la meilleure option, car elle utilise le port HTTPS standard et un seul point de terminaison. Toutefois, il existe un compromis en termes de performances : le mode passerelle implique un tronçon réseau supplémentaire chaque fois que les données sont lues ou écrites dans Azure Cosmos DB. Étant donné que le mode DirectHttps implique moins de tronçons réseaux, les performances sont meilleures. 

    Le SDK Java utilise HTTPS comme protocole de transport. HTTPS utilise SSL pour l’authentification initiale et le chiffrement du trafic. Quand vous utilisez le SDK Java, seul le port HTTPS 443 doit être ouvert. 

    Le mode de connexion est configuré pendant la construction de l’instance DocumentClient avec le paramètre ConnectionPolicy. 

    ```Java
    public ConnectionPolicy getConnectionPolicy() {
        ConnectionPolicy policy = new ConnectionPolicy();
        policy.setConnectionMode(ConnectionMode.DirectHttps);
        policy.setMaxPoolSize(1000);
        return policy;
    }
        
    ConnectionPolicy connectionPolicy = new ConnectionPolicy();
    DocumentClient client = new DocumentClient(HOST, MASTER_KEY, connectionPolicy, null);
    ```

    ![Illustration de la stratégie de connexion Azure Cosmos DB](./media/performance-tips-java/connection-policy.png)

   <a id="same-region"></a>
2. **Colocalisation des clients dans la même région Azure pour les performances**

    Dans la mesure du possible, placez toutes les applications appelant Azure Cosmos DB dans la même région que la base de données Azure Cosmos DB. Pour une comparaison approximative, les appels à Azure Cosmos DB dans la même région s’effectuent en 1 à 2 ms, mais la latence entre les côtes Ouest et Est des États-Unis est supérieure à 50 ms. Cette latence peut probablement varier d’une requête à l’autre, en fonction de l’itinéraire utilisé par la requête lorsqu’elle passe du client à la limite du centre de données Azure. Pour obtenir la latence la plus faible possible, l’application appelante doit être située dans la même région Azure que le point de terminaison Azure Cosmos DB configuré. Pour obtenir la liste des régions disponibles, voir [Régions Azure](https://azure.microsoft.com/regions/#services).

    ![Illustration de la stratégie de connexion Azure Cosmos DB](./media/performance-tips/same-region.png)
   
## <a name="sdk-usage"></a>Utilisation du kit de développement logiciel (SDK)
1. **Installation du kit de développement logiciel (SDK) le plus récent**

    Les SDK Azure Cosmos DB sont constamment améliorés pour fournir des performances optimales. Consultez les pages du [SDK Azure Cosmos DB](documentdb-sdk-java.md) pour déterminer quel est le SDK le plus récent et passer en revue les améliorations.
2. **Utilisation d’un client Azure Cosmos DB singleton pour la durée de vie de votre application**

    Chaque instance de [DocumentClient](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client) est thread-safe et effectue une gestion des connexions efficace et une mise en cache d’adresses quand le mode direct est sélectionné. Pour permettre une gestion des connexions efficace et améliorer les performances par DocumentClient, nous vous recommandons d’utiliser une seule instance de DocumentClient par AppDomain pour la durée de vie de l’application.

   <a id="max-connection"></a>
3. **Augmentation de MaxPoolSize par hôte quand le mode passerelle est utilisé**

    Les requêtes Azure Cosmos DB sont effectuées par le biais de HTTPS/REST durant l’utilisation du mode passerelle et sont soumises aux limites de connexion par défaut par nom d’hôte ou adresse IP. Vous devrez peut-être définir MaxPoolSize sur une valeur plus élevée (200 à 1000) afin que la bibliothèque cliente puisse utiliser plusieurs connexions simultanées à Azure Cosmos DB. Dans le SDK Java, la valeur par défaut de [ConnectionPolicy.getMaxPoolSize](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._connection_policy.gsetmaxpoolsize) est 100. Utilisez [setMaxPoolSize]( https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._connection_policy.setmaxpoolsize) pour changer la valeur.

4. **Paramétrage des requêtes parallèles pour les collections partitionnées**

    La version 1.9.0 et les versions ultérieures du SDK Java Azure Cosmos DB prennent en charge les requêtes parallèles, qui vous permettent d’interroger une collection partitionnée en parallèle (pour plus d’informations, voir [Utilisation des kits de développement logiciel (SDK)](sql-api-partition-data.md#working-with-the-azure-cosmos-db-sdks) et les [exemples de code](https://github.com/Azure/azure-documentdb-java/tree/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples) connexes). Les requêtes parallèles sont conçues pour améliorer la latence des requêtes et le débit sur leur équivalent série.

    (a) Les requêtes parallèles ***Tuning setMaxDegreeOfParallelism\:*** interrogent plusieurs partitions en parallèle. Les données d’une collection partitionnée individuelle sont toutefois extraites en série dans le cadre de la requête. Utilisez donc le paramètre [setMaxDegreeOfParallelism](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._feed_options.setmaxdegreeofparallelism) pour définir le nombre de partitions qui augmente les chances de résultats de la requête, sous réserve que toutes les autres conditions système restent inchangées. Si vous ne connaissez pas le nombre de partitions, vous pouvez utiliser le paramètre setMaxDegreeOfParallelism pour définir un nombre élevé, et le système sélectionne le minimum (nombre de partitions, entrée fournie par l’utilisateur) comme degré maximal de parallélisme. 

    Il est important de noter que les requêtes parallèles produisent de meilleurs résultats si les données sont réparties de manière homogène entre toutes les partitions. Si la collection est partitionnée de telle façon que toutes les données retournées par une requête, ou une grande partie d’entre elles, sont concentrées sur quelques partitions (une partition dans le pire des cas), les performances de la requête sont altérées par ces partitions.

    (b) La requête parallèle ***Tuning setMaxBufferedItemCount\:*** pré-extrait les résultats tandis que le lot de résultats courant est en cours de traitement par le client. La pré-extraction permet d’améliorer la latence globale d’une requête. setMaxBufferedItemCount limite le nombre de résultats pré-extraits. Définir le paramètre [setMaxBufferedItemCount](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._feed_options.setmaxbuffereditemcount) sur le nombre de résultats attendu (ou un nombre plus élevé) permet à la requête d’optimiser la pré-extraction.

    La pré-extraction fonctionne de la même façon, quel que soit le paramètre MaxDegreeOfParallelism, et il existe une seule mémoire tampon pour les données de toutes les partitions.  

5. **Implémentation de l’interruption à intervalles définis par getRetryAfterInMilliseconds**

    Lors du test de performances, vous devez augmenter la charge jusqu’à une limite d’un petit nombre de requêtes. En cas de limitation, l’application cliente doit s’interrompre à la limitation pour l’intervalle de nouvelle tentative spécifié sur le serveur Le respect de l’interruption garantit un temps d’attente minimal entre chaque tentative. La prise en charge de la stratégie de nouvelle tentative est incluse dans les versions 1.8.0 et supérieures du [SDK Java](documentdb-sdk-java.md). Pour plus d’informations, consultez la section [Dépassement des limites de débit réservé](request-units.md#RequestRateTooLarge) et [getRetryAfterInMilliseconds](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client_exception.getretryafterinmilliseconds).
6. **Augmentation de la taille des instances de votre charge de travail cliente**

    Si vous effectuez des tests à des niveaux de débit élevé (> 50 000 RU/s), l’application cliente peut devenir un goulet d’étranglement en raison du plafonnement sur l’utilisation du processeur ou du réseau. Si vous atteignez ce point, vous pouvez continuer à augmenter le compte Azure Cosmos DB en augmentant la taille des instances de vos applications clientes sur plusieurs serveurs.

7. **Utilisation de l’adressage en fonction du nom**

    Utilisez l’adressage en fonction du nom, où les liens ont le format `dbs/MyDatabaseId/colls/MyCollectionId/docs/MyDocumentId`, au lieu de liens vers lui-même (_self), qui ont le format `dbs/<database_rid>/colls/<collection_rid>/docs/<document_rid>`, pour éviter l’extraction des ID de ressource de toutes les ressources utilisées pour construire le lien. En outre, ces ressources étant recréés (éventuellement avec le même nom), leur mise en cache peut s’avérer superflue.

   <a id="tune-page-size"></a>
8. **Réglage de la taille de la page des flux de lecture/requêtes pour de meilleures performances**

    Pendant une lecture groupée de documents à l’aide de la fonctionnalité de flux de lecture (par exemple, [readDocuments]( https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.readdocuments#com_microsoft_azure_documentdb__document_client_readDocuments_String_FeedOptions_c)) ou l’émission d’une requête SQL, les résultats sont retournés de façon segmentée si le jeu de résultats est trop grand. Par défaut, les résultats sont retournés dans des segments de 100 éléments ou de 1 Mo, selon la limite atteinte en premier.

    Afin de réduire le nombre de boucles réseau nécessaires pour récupérer tous les résultats applicables, vous pouvez augmenter la taille de la page à 1000 résultats à l’aide de l’en-tête de requête [x-ms-max-item-count](https://docs.microsoft.com/rest/api/documentdb/common-documentdb-rest-request-headers). Si vous avez besoin d’afficher uniquement quelques résultats, (par exemple, si votre interface utilisateur ou API d’application retourne seulement 10 résultats à la fois), vous pouvez également réduire la taille de la page à 10 résultats, afin de baisser le débit consommé pour les lectures et requêtes.

    Vous pouvez également définir la taille de la page à l’aide de la [méthode setPageSize](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._feed_options_base.setpagesize#com_microsoft_azure_documentdb__feed_options_base_setPageSize_Integer).

## <a name="indexing-policy"></a>Stratégie d'indexation
 
1. **Exclusion des chemins d’accès inutilisés de l’indexation pour des écritures plus rapides**

    La stratégie d’indexation d’Azure Cosmos DB vous permet de spécifier les chemins d’accès de document à inclure ou exclure de l’indexation en tirant parti des chemins d’accès d’indexation ([setIncludedPaths](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._indexing_policy.setincludedpaths) et [setExcludedPaths](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._indexing_policy.setexcludedpaths)). L’utilisation des chemins d’accès d’indexation peut offrir des performances d’écriture améliorées et réduire le stockage d’index pour les scénarios dans lesquels les modèles de requête sont connus d’avance, puisque les coûts d’indexation sont directement liés au nombre de chemins d’accès uniques indexés.  Par exemple, le code suivant montre comment exclure toute une section de documents (également appelée sous-arborescence) de l’indexation à l’aide du caractère générique « * ».

    ```Java
    Index numberIndex = Index.Range(DataType.Number);
    numberIndex.set("precision", -1);
    indexes.add(numberIndex);
    includedPath.setIndexes(indexes);
    includedPaths.add(includedPath);
    indexingPolicy.setIncludedPaths(includedPaths);
    collectionDefinition.setIndexingPolicy(indexingPolicy);
    ```

    Pour plus d’informations, consultez [Stratégies d’indexation d’Azure Cosmos DB](indexing-policies.md).

## <a name="throughput"></a>Throughput
<a id="measure-rus"></a>

1. **Mesure et réglage pour réduire l’utilisation d’unités de requête par seconde**

    Azure Cosmos DB propose un riche ensemble d’opérations de base de données, dont les requêtes hiérarchiques et relationnelles avec les fonctions définies par l’utilisateur, les procédures stockées et les déclencheurs, qui fonctionnent toutes au niveau des documents d’une collection de base de données. Le coût associé à chacune de ces opérations varie en fonction du processeur, des E/S et de la mémoire nécessaires à l’exécution de l’opération. Plutôt que de vous soucier de la gestion des ressources matérielles, vous pouvez considérer une unité de demande comme une mesure unique des ressources nécessaires à l'exécution des opérations de base de données et à la réponse à la demande de l'application.

    Le débit est provisionné en fonction du nombre [d’unités de requête](request-units.md) défini pour chaque conteneur. La consommation d'unités de demande est évaluée en fonction d'un taux par seconde. Les applications qui dépassent le taux d’unités de requête configuré pour le conteneur associé sont limitées jusqu’à ce que le taux soit inférieur au niveau configuré pour le conteneur. Si votre application requiert un niveau de débit plus élevé, vous pouvez augmenter le débit en provisionnant des unités de requête supplémentaires. 

    La complexité d’une requête a un impact sur le nombre d’unités de requête consommées pour une opération. Le nombre de prédicats, la nature des prédicats, le nombre de fonctions définies par l’utilisateur et la taille du jeu de données sources ont tous une influence sur le coût des opérations de requête.

    Pour mesurer les frais de l’opération (création, mise à jour ou suppression), inspectez l’en-tête [x-ms-request-charge](https://docs.microsoft.com/rest/api/documentdb/common-documentdb-rest-response-headers) (ou la propriété RequestCharge équivalente dans [ResourceResponse<T>](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._resource_response) ou [FeedResponse<T>](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._feed_response)) afin de déterminer le nombre d’unités de requête consommées par ces opérations.

    ```Java
    ResourceResponse<Document> response = client.createDocument(collectionLink, documentDefinition, null, false);

    response.getRequestCharge();
    ```             

    Les frais de la requête retournée dans cet en-tête correspondent à une fraction du débit provisionné. Par exemple, si 2 000 RU/seconde sont provisionnées et que la requête ci-dessus retourne 1000 documents de 1 Ko, le coût de l’opération est de 1000. Par conséquent, en une seconde, le serveur honore uniquement deux requêtes avant de limiter les requêtes suivantes. Pour plus d’informations, consultez [Unités de requête](request-units.md) et la [calculatrice d’unités de requête](https://www.documentdb.com/capacityplanner).
<a id="429"></a>
2. **Gestion de la limite de taux/du taux de requête trop importants**

    Lorsqu’un client tente de dépasser le débit réservé pour un compte, les performances au niveau du serveur ne sont pas affectées et la capacité de débit n’est pas utilisée au-delà du niveau réservé. Le serveur met fin à la requête de manière préventive avec RequestRateTooLarge (code d’état HTTP 429) et il retourne l’en-tête [x-ms-retry-after-ms](https://docs.microsoft.com/rest/api/documentdb/common-documentdb-rest-response-headers) indiquant la durée, en millisecondes, pendant laquelle l’utilisateur doit attendre avant de réessayer.

        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100

    Les kits de développement logiciel (SDK) interceptent tous implicitement cette réponse, respectent l’en-tête retry-after spécifiée par le serveur, puis relancent la requête. La tentative suivante réussira toujours, sauf si plusieurs clients accèdent simultanément à votre compte.

    Si plusieurs clients opèrent simultanément au-delà du taux de requête, le nombre de nouvelles tentatives par défaut actuellement défini sur 9 en interne par le client peut ne pas suffire. Dans ce cas, le client envoie une exception [DocumentClientException](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client_exception) avec le code d’état 429 à l’application. Vous pouvez changer le nombre de nouvelles tentatives par défaut en utilisant [setRetryOptions](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._connection_policy.setretryoptions) sur l’instance [ConnectionPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._connection_policy). Par défaut, la DocumentClientException avec le code d’état 429 est retournée après un temps d’attente cumulé de 30 secondes si la requête continue à fonctionner au-dessus du taux de requête. Cela se produit même lorsque le nombre de nouvelles tentatives actuel est inférieur au nombre maximal de nouvelles tentatives, qu’il s’agisse de la valeur par défaut de 9 ou d’une valeur définie par l’utilisateur.

    Alors que le comportement de nouvelle tentative automatique permet d’améliorer la résilience et la facilité d’utilisation pour la plupart des applications, il peut se révéler contradictoire lors de l’exécution de tests de performances, en particulier lors de la mesure de la latence.  La latence client observée atteindra un pic si l’expérience atteint la limite de serveur et oblige le kit de développement logiciel (SDK) client à effectuer une nouvelle tentative en silence. Pour éviter des pics de latence lors des expériences de performances, mesurez la charge renvoyée par chaque opération et assurez-vous que les requêtes fonctionnent en dessous du taux de requête réservé. Pour plus d’informations, consultez [Unités de requête](request-units.md).
3. **Conception de documents plus petits pour un débit plus élevé**

    Les frais de requête (le coût de traitement de requête) d’une opération donnée sont directement liés à la taille du document. Des opérations sur des documents volumineux coûtent plus cher que des opérations sur de petits documents.

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur la conception de votre application pour une mise à l’échelle et de hautes performances, consultez [Partitionnement, clés de partition et mise à l’échelle dans Cosmos DB](partition-data.md).
