---
title: "Architectures de base de données à multiples maîtres avec Azure Cosmos DB | Microsoft Docs"
description: "En savoir plus sur la conception des architectures d’application avec les lectures et écritures locales dans plusieurs régions géographiques avec Azure Cosmos DB."
services: cosmos-db
documentationcenter: 
author: arramac
manager: jhubbard
editor: 
ms.assetid: 706ced74-ea67-45dd-a7de-666c3c893687
ms.service: cosmos-db
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/23/2017
ms.author: arramac
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e02b286db42d8a9de8f1df8263f40c3732484038
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2017
---
# <a name="multi-master-globally-replicated-database-architectures-with-azure-cosmos-db"></a>Architectures de base de données à multiples maîtres répliquées de façon globale avec Azure Cosmos DB
Azure Cosmos DB prend en charge la [réplication globale](distribute-data-globally.md) clé en main, qui vous permet de distribuer les données dans plusieurs régions avec accès à faible latence n’importe où dans la charge de travail. Ce modèle est généralement utilisé pour les charges de travail éditeur/consommateur, où un enregistreur existe dans une région géographique unique et des lecteurs sont distribués mondialement dans d’autres régions (lecture). 

Vous pouvez également utiliser la prise en charge de la réplication globale d’Azure Cosmos DB pour créer des applications dans lesquelles des enregistreurs et des lecteurs sont distribués mondialement. Ce document décrit un modèle qui permet d’obtenir un accès en écriture locale et en lecture locale pour les enregistreurs utilisant Azure Cosmos DB.

## <a id="ExampleScenario"></a>Publication de contenu : un exemple de scénario
Examinons un scénario réel pour décrire la manière dont vous pouvez utiliser des modèles de lecture/écriture mondialement distribués multirégions/multimaîtres avec Azure Cosmos DB. Prenons l’exemple d’une plateforme de contenu reposant sur Azure Cosmos DB. Voici certaines exigences que cette plateforme doit respecter pour une expérience utilisateur exceptionnelle pour les éditeurs et les consommateurs.

* Les auteurs et les abonnés sont répartis dans le monde entier 
* Les auteurs doivent publier (écriture) les articles dans leur région (la plus proche)
* Les auteurs ont des lecteurs/abonnés de leurs articles répartis dans le monde entier. 
* Les abonnés doivent recevoir une notification lorsque de nouveaux articles sont publiés.
* Les abonnés doivent être en mesure de lire les articles de leur région. Ils doivent également être en mesure d’ajouter des avis concernant ces articles. 
* Tout le monde, y compris l’auteur des articles, doit être en mesure de visualiser tous les avis liés aux articles à partir d’une région. 

En supposant qu’il y a des millions de consommateurs et d’éditeurs avec des milliards d’articles, nous devrons bientôt faire face à des problèmes de mise à l’échelle et de garantie de localité d’accès. Comme avec la plupart des problèmes d’évolutivité, la solution réside dans une bonne stratégie de partitionnement. Ensuite, nous verrons comment modéliser des articles, des avis et des notifications sous forme de documents, configurer des comptes Azure Cosmos DB et implémenter une couche d’accès aux données. 

Si vous souhaitez en savoir plus sur le partitionnement et les clés de partition, consultez [Partitionnement et mise à l’échelle dans Azure Cosmos DB](partition-data.md).

## <a id="ModelingNotifications"></a>Modélisation des notifications
Les notifications sont des flux de données spécifiques à un utilisateur. Par conséquent, les modèles d’accès pour les documents de notifications sont toujours destinés à un utilisateur unique. Ainsi, vous pouvez « publier une notification à un utilisateur » ou « extraire toutes les notifications pour un utilisateur donné ». Ainsi, le choix optimal de clé de partitionnement pour ce type est `UserId`.

    class Notification 
    { 
        // Unique ID for Notification. 
        public string Id { get; set; }

        // The user Id for which notification is addressed to. 
        public string UserId { get; set; }

        // The partition Key for the resource. 
        public string PartitionKey 
        { 
            get 
            { 
                return this.UserId; 
            }
        }

        // Subscription for which this notification is raised. 
        public string SubscriptionFilter { get; set; }

        // Subject of the notification. 
        public string ArticleId { get; set; } 
    }

## <a id="ModelingSubscriptions"></a>Modélisation des abonnements
Les abonnements peuvent être créés selon différents critères comme une catégorie spécifique d’articles d’intérêt ou un éditeur spécifique. Par conséquent, `SubscriptionFilter` constitue un bon choix pour la clé de partition.

    class Subscriptions 
    { 
        // Unique ID for Subscription 
        public string Id { get; set; }

        // Subscription source. Could be Author | Category etc. 
        public string SubscriptionFilter { get; set; }

        // subscribing User. 
        public string UserId { get; set; }

        public string PartitionKey 
        { 
            get 
            { 
                return this.SubscriptionFilter; 
            } 
        } 
    }

## <a id="ModelingArticles"></a>Modélisation des articles
Une fois qu’un article est identifié par le biais des notifications, les requêtes suivantes sont généralement basés sur `Article.Id`. En choisissant `Article.Id` en tant que partition, la clé fournit la meilleure distribution pour le stockage des articles à l’intérieur d’une collection Azure Cosmos DB. 

    class Article 
    { 
        // Unique ID for Article 
        public string Id { get; set; }
        
        public string PartitionKey 
        { 
            get 
            { 
                return this.Id; 
            } 
        }
        
        // Author of the article
        public string Author { get; set; }

        // Category/genre of the article
        public string Category { get; set; }

        // Tags associated with the article
        public string[] Tags { get; set; }

        // Title of the article
        public string Title { get; set; }
        
        //... 
    }

## <a id="ModelingReviews"></a>Modélisation des avis
Les avis sont principalement écrits et lus dans le contexte de l’article. Choisir `ArticleId` en tant que clé de partition fournit une meilleure distribution et un accès efficace aux avis associés à l’article. 

    class Review 
    { 
        // Unique ID for Review 
        public string Id { get; set; }

        // Article Id of the review 
        public string ArticleId { get; set; }

        public string PartitionKey 
        { 
            get 
            { 
                return this.ArticleId; 
            } 
        }
        
        //Reviewer Id 
        public string UserId { get; set; }
        public string ReviewText { get; set; }
        
        public int Rating { get; set; } }
    }

## <a id="DataAccessMethods"></a>Méthodes de couche d’accès aux données
Maintenant, examinons les méthodes d’accès aux données principales que nous devons mettre en œuvre. Voici une liste des méthodes dont `ContentPublishDatabase` a besoin :

    class ContentPublishDatabase 
    { 
        public async Task CreateSubscriptionAsync(string userId, string category);
    
        public async Task<IEnumerable<Notification>> ReadNotificationFeedAsync(string userId);
    
        public async Task<Article> ReadArticleAsync(string articleId);
    
        public async Task WriteReviewAsync(string articleId, string userId, string reviewText, int rating);
    
        public async Task<IEnumerable<Review>> ReadReviewsAsync(string articleId); 
    }

## <a id="Architecture"></a>Configuration du compte Azure Cosmos DB
Pour garantir les lectures et écritures locales, nous devons partitionner les données, non seulement sur la clé de partition de clé, mais également selon le modèle d’accès géographique dans les régions. Le modèle repose sur l’existence d’un compte de base de données Azure Cosmos DB géorépliqué pour chaque région. Voici, par exemple, une configuration pour les écritures multirégions avec deux régions :

| Nom du compte | Région d’écriture | Région de lecture |
| --- | --- | --- |
| `contentpubdatabase-usa.documents.azure.com` | `West US` |`North Europe` |
| `contentpubdatabase-europe.documents.azure.com` | `North Europe` |`West US` |

Le diagramme suivant montre comment les lectures et écritures sont effectuées dans une application standard avec cette configuration :

![Architecture à multiples maîtres Azure Cosmos DB](./media/multi-region-writers/multi-master.png)

Voici un extrait de code montrant comment initialiser les clients dans une couche d’accès aux données en cours d’exécution dans la région `West US`.
    
    ConnectionPolicy writeClientPolicy = new ConnectionPolicy { ConnectionMode = ConnectionMode.Direct, ConnectionProtocol = Protocol.Tcp };
    writeClientPolicy.PreferredLocations.Add(LocationNames.WestUS);
    writeClientPolicy.PreferredLocations.Add(LocationNames.NorthEurope);

    DocumentClient writeClient = new DocumentClient(
        new Uri("https://contentpubdatabase-usa.documents.azure.com"), 
        writeRegionAuthKey,
        writeClientPolicy);

    ConnectionPolicy readClientPolicy = new ConnectionPolicy { ConnectionMode = ConnectionMode.Direct, ConnectionProtocol = Protocol.Tcp };
    readClientPolicy.PreferredLocations.Add(LocationNames.NorthEurope);
    readClientPolicy.PreferredLocations.Add(LocationNames.WestUS);

    DocumentClient readClient = new DocumentClient(
        new Uri("https://contentpubdatabase-europe.documents.azure.com"),
        readRegionAuthKey,
        readClientPolicy);

Avec la configuration précédente, la couche d’accès aux données peut transférer toutes les écritures vers le compte local basé selon l’emplacement où il est déployé. Les lectures sont effectuées depuis les deux comptes pour une vue globale des données. Cette approche peut être étendue à autant de régions que nécessaire. Voici, par exemple, une configuration avec trois régions :

| Nom du compte | Région d’écriture | Région de lecture 1 | Région de lecture 2 |
| --- | --- | --- | --- |
| `contentpubdatabase-usa.documents.azure.com` | `West US` |`North Europe` |`Southeast Asia` |
| `contentpubdatabase-europe.documents.azure.com` | `North Europe` |`West US` |`Southeast Asia` |
| `contentpubdatabase-asia.documents.azure.com` | `Southeast Asia` |`North Europe` |`West US` |

## <a id="DataAccessImplementation"></a>Mise en œuvre de la couche d’accès aux données
Maintenant, examinons la mise en œuvre de la couche d’accès aux données pour une application avec deux régions en écriture. La couche d’accès aux données doit mettre en œuvre les opérations suivantes :

* Créer plusieurs instances de `DocumentClient` pour chaque compte. Avec deux régions, chaque instance de la couche d’accès aux données possède un `writeClient` et `readClient`. 
* En fonction de la région déployée de l’application, configurez des points de terminaison pour `writeclient` et `readClient`. Par exemple, la couche d’accès aux données déployée dans `West US` utilise `contentpubdatabase-usa.documents.azure.com` pour effectuer des écritures. La couche DAL déployée dans `NorthEurope` utilise `contentpubdatabase-europ.documents.azure.com` pour les écritures.

Avec la configuration précédente, les méthodes d’accès aux données peuvent être mises en œuvre. Les opérations d’écriture transfèrent l’écriture au `writeClient` correspondant.

    public async Task CreateSubscriptionAsync(string userId, string category)
    {
        await this.writeClient.CreateDocumentAsync(this.contentCollection, new Subscriptions
        {
            UserId = userId,
            SubscriptionFilter = category
        });
    }

    public async Task WriteReviewAsync(string articleId, string userId, string reviewText, int rating)
    {
        await this.writeClient.CreateDocumentAsync(this.contentCollection, new Review
        {
            UserId = userId,
            ArticleId = articleId,
            ReviewText = reviewText,
            Rating = rating
        });
    }

Pour lire les notifications et les avis, vous devez lire à partir des régions et associer les résultats comme indiqué dans l’extrait suivant :

    public async Task<IEnumerable<Notification>> ReadNotificationFeedAsync(string userId)
    {
        IDocumentQuery<Notification> writeAccountNotification = (
            from notification in this.writeClient.CreateDocumentQuery<Notification>(this.contentCollection) 
            where notification.UserId == userId 
            select notification).AsDocumentQuery();
        
        IDocumentQuery<Notification> readAccountNotification = (
            from notification in this.readClient.CreateDocumentQuery<Notification>(this.contentCollection) 
            where notification.UserId == userId 
            select notification).AsDocumentQuery();

        List<Notification> notifications = new List<Notification>();

        while (writeAccountNotification.HasMoreResults || readAccountNotification.HasMoreResults)
        {
            IList<Task<FeedResponse<Notification>>> results = new List<Task<FeedResponse<Notification>>>();

            if (writeAccountNotification.HasMoreResults)
            {
                results.Add(writeAccountNotification.ExecuteNextAsync<Notification>());
            }

            if (readAccountNotification.HasMoreResults)
            {
                results.Add(readAccountNotification.ExecuteNextAsync<Notification>());
            }

            IList<FeedResponse<Notification>> notificationFeedResult = await Task.WhenAll(results);

            foreach (FeedResponse<Notification> feed in notificationFeedResult)
            {
                notifications.AddRange(feed);
            }
        }
        return notifications;
    }

    public async Task<IEnumerable<Review>> ReadReviewsAsync(string articleId)
    {
        IDocumentQuery<Review> writeAccountReviews = (
            from review in this.writeClient.CreateDocumentQuery<Review>(this.contentCollection) 
            where review.ArticleId == articleId 
            select review).AsDocumentQuery();
        
        IDocumentQuery<Review> readAccountReviews = (
            from review in this.readClient.CreateDocumentQuery<Review>(this.contentCollection) 
            where review.ArticleId == articleId 
            select review).AsDocumentQuery();

        List<Review> reviews = new List<Review>();
        
        while (writeAccountReviews.HasMoreResults || readAccountReviews.HasMoreResults)
        {
            IList<Task<FeedResponse<Review>>> results = new List<Task<FeedResponse<Review>>>();

            if (writeAccountReviews.HasMoreResults)
            {
                results.Add(writeAccountReviews.ExecuteNextAsync<Review>());
            }

            if (readAccountReviews.HasMoreResults)
            {
                results.Add(readAccountReviews.ExecuteNextAsync<Review>());
            }

            IList<FeedResponse<Review>> notificationFeedResult = await Task.WhenAll(results);

            foreach (FeedResponse<Review> feed in notificationFeedResult)
            {
                reviews.AddRange(feed);
            }
        }

        return reviews;
    }

Par conséquent, en choisissant une bonne clé de partitionnement et le partitionnement statique basé sur les comptes, vous pouvez effectuer des lectures et écritures locales multirégions à l’aide d’Azure Cosmos DB.

## <a id="NextSteps"></a>Étapes suivantes
Dans cet article, nous avons décrit comment vous pouvez utiliser des modèles lecture/écriture multirégions mondialement distribués avec Azure Cosmos DB à l’aide de la publication de contenu comme exemple de scénario.

* Découvrez-en plus sur la manière dont Azure Cosmos DB prend en charge la [distribution mondiale](distribute-data-globally.md)
* En savoir plus sur les [basculements manuels et automatiques dans Azure Cosmos DB](regional-failover.md)
* Découvrez-en plus sur [la cohérence globale avec Azure Cosmos DB](consistency-levels.md)
* Développer en mode multirégions à l’aide de [l’API SQL - Azure Cosmos DB](tutorial-global-distribution-sql-api.md)
* Développer en mode multirégions à l’aide de [l’API MongoDB - Azure Cosmos DB](tutorial-global-distribution-MongoDB.md)
* Développer en mode multirégions à l’aide de [l’API Table - Azure Cosmos DB](tutorial-global-distribution-table.md)
