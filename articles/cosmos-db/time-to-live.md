---
title: Faire expirer des données dans Cosmos DB avec la durée de vie | Documents Microsoft
description: Avec la TTL, Microsoft Azure Cosmos DB offre la possibilité de vider automatiquement les documents du système après une période déterminée.
services: cosmos-db
documentationcenter: ''
keywords: durée de vie
author: arramac
manager: jhubbard
editor: ''
ms.assetid: 25fcbbda-71f7-414a-bf57-d8671358ca3f
ms.service: cosmos-db
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/29/2017
ms.author: arramac
ms.openlocfilehash: 6f8ce4e270b94bf1043c27ba879878e20372ffe7
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2018
---
# <a name="expire-data-in-azure-cosmos-db-collections-automatically-with-time-to-live"></a>Faire expirer des données dans des collections Cosmos DB automatiquement avec la durée de vie
Les applications peuvent générer et stocker de grandes quantités de données. Certaines de ces données, telles que les données d’événement générées par la machine, les journaux et les informations de session utilisateur, sont utiles uniquement pendant une certaine période. Une fois les données trop nombreuses par rapport aux besoins de l’application, vous pouvez les vider et réduire les besoins de stockage d’une application.

Avec la « durée de vie » (TTL, Time to Live), Microsoft Azure Cosmos DB offre la possibilité de vider automatiquement les documents de la base de données après une période déterminée. La durée de vie par défaut peut être définie au niveau de la collection et être substituée par document. Une fois la TTL définie, soit en tant que valeur par défaut de la collection, soit au niveau du document, Cosmos DB supprime automatiquement les documents existant une fois cette période (en secondes) écoulée depuis leur dernière modification.

Dans Cosmos DB, la durée de vie utilise un décalage par rapport au moment où le document a été modifié pour la dernière fois. Pour ce faire, il utilise le champ `_ts` qui existe sur tous les documents. Le champ _ts est un horodateur d’époque de style Unix représentant la date et l’heure. Le champ `_ts` est mis à jour à chaque modification d’un document. 

## <a name="ttl-behavior"></a>Comportement de la TTL
La fonction TTL est contrôlée par les propriétés TTL à deux niveaux : au niveau de la collection et au niveau du document. Les valeurs sont définies en secondes et sont traitées en tant qu’écart par rapport à l’horodatage de dernière modification du document (`_ts`).

1. DefaultTTL pour la collection
   
   * Si ce paramètre est manquant (ou a la valeur null), les documents ne sont pas supprimés automatiquement.
   * Si ce paramètre est présent et a la valeur « -1 » (infinie), les documents n’expirent pas par défaut.
   * Si ce paramètre est présent et que sa valeur est un nombre (« n »), les documents expirent « n » secondes après la dernière modification.
2. TTL pour les documents : 
   
   * La propriété s’applique uniquement si le paramètre DefaultTTL est présent pour la collection parente.
   * Elle remplace la valeur DefaultTTL de la collection parente.

Dès que le document a expiré (`ttl` + `_ts` <= heure actuelle du serveur), le document est marqué comme « expiré ». Aucune opération n’est autorisée sur ces documents une fois ce délai écoulé, et les documents sont exclus des résultats de toutes les requêtes effectuées. Les documents sont physiquement supprimés du système et sont supprimés en arrière-plan de façon opportuniste ultérieurement. Ceci ne consomme aucune [unité de requête](request-units.md) du budget de la collection.

La logique ci-dessus peut être représentée dans le tableau suivant :

|  | DefaultTTL manquante/non définie sur la collection | DefaultTTL = -1 sur la collection | DefaultTTL = « n » sur la collection |
| --- |:--- |:--- |:--- |
| TTL manquante sur le document |Rien à substituer au niveau du document, car aucun concept de TTL n’existe pour le document et la collection. |Aucun document de cette collection n’expire. |Les documents de cette collection expireront une fois l’intervalle n écoulé. |
| TTL = -1 sur le document |Rien à substituer au niveau du document, car la collection ne définit pas la propriété DefaultTTL qu’un document peut substituer. La TTL sur un document n’est pas interprétée par le système. |Aucun document de cette collection n’expire. |Le document avec TTL=-1 dans cette collection n’expire jamais. Tous les autres documents expirent après l’intervalle « n ». |
| TTL = n sur le document |Rien à substituer au niveau du document. La TTL sur un document n’est pas interprétée par le système. |Le document avec TTL = n expire après l’intervalle n, en secondes. D’autres documents héritent de l’intervalle -1 et n’expirent jamais. |Le document avec TTL = n expire après l’intervalle n, en secondes. D’autres documents héritent de l’intervalle « n » de la collection. |

## <a name="configuring-ttl"></a>Configuration de la TTL
Par défaut, la durée de vie est désactivée dans toutes les collections Cosmos DB et sur tous les documents. La durée de vie (TTL) peut être définie par programme ou dans le portail Azure, dans la section **Paramètres** de la collection. 

## <a name="enabling-ttl"></a>Activation de la TTL
Pour activer la TTL sur une collection ou sur les documents d’une collection, vous devez définir la propriété DefaultTTL d’une collection sur -1 ou un nombre positif non nul. Si vous définissez DefaultTTL sur -1, tous les documents de la collection auront une durée de vie infinie par défaut. Cependant, le service Cosmos DB doit alors surveiller la collection afin d’identifier les documents pour lesquels cette valeur par défaut a été remplacée.

    DocumentCollection collectionDefinition = new DocumentCollection();
    collectionDefinition.Id = "orders";
    collectionDefinition.PartitionKey.Paths.Add("/customerId");
    collectionDefinition.DefaultTimeToLive =-1; //never expire by default

    DocumentCollection ttlEnabledCollection = await client.CreateDocumentCollectionAsync(
        UriFactory.CreateDatabaseUri(databaseName),
        collectionDefinition,
        new RequestOptions { OfferThroughput = 20000 });

## <a name="configuring-default-ttl-on-a-collection"></a>Configuration de la TTL par défaut sur une collection
Vous pouvez configurer une durée de vie par défaut au niveau de la collection. Pour définir la durée de vie sur une collection, vous devez fournir un nombre positif non nul qui indique, en secondes, le délai d’expiration de tous les documents de la collection après l’horodatage de dernière modification du document (`_ts`). Vous pouvez également définir la valeur par défaut sur -1, ce qui implique que tous les documents insérés dans la collection auront une durée de vie infinie par défaut.

    DocumentCollection collectionDefinition = new DocumentCollection();
    collectionDefinition.Id = "orders";
    collectionDefinition.PartitionKey.Paths.Add("/customerId");
    collectionDefinition.DefaultTimeToLive = 90 * 60 * 60 * 24; // expire all documents after 90 days
    
    DocumentCollection ttlEnabledCollection = await client.CreateDocumentCollectionAsync(
        "/dbs/salesdb",
        collectionDefinition,
        new RequestOptions { OfferThroughput = 20000 });


## <a name="setting-ttl-on-a-document"></a>Définition de la TTL sur un document
En plus de définir une TTL par défaut sur une collection, vous pouvez définir une TTL spécifique au niveau d’un document. Cela remplace la valeur par défaut de la collection.

* Pour définir la durée de vie sur un document, vous devez fournir un nombre positif non nul qui indique, en secondes, le délai d’expiration du document après l’horodatage de dernière modification du document (`_ts`).
* Si un document n’a pas de champ TTL, la valeur par défaut de la collection s’applique.
* Si la TTL est désactivée au niveau de la collection, le champ TTL du document sera ignoré jusqu’à ce que la TTL soit de nouveau activée sur la collection.

Voici un extrait de code montrant comment définir la date d’expiration de la durée de vie dans un document :

    // Include a property that serializes to "ttl" in JSON
    public class SalesOrder
    {
        [JsonProperty(PropertyName = "id")]
        public string Id { get; set; }
        
        [JsonProperty(PropertyName="cid")]
        public string CustomerId { get; set; }
        
        // used to set expiration policy
        [JsonProperty(PropertyName = "ttl", NullValueHandling = NullValueHandling.Ignore)]
        public int? TimeToLive { get; set; }
        
        //...
    }
    
    // Set the value to the expiration in seconds
    SalesOrder salesOrder = new SalesOrder
    {
        Id = "SO05",
        CustomerId = "CO18009186470",
        TimeToLive = 60 * 60 * 24 * 30;  // Expire sales orders in 30 days 
    };


## <a name="extending-ttl-on-an-existing-document"></a>Extension de la TTL sur un document existant
Vous pouvez réinitialiser la TTL d’un document en effectuant une opération d’écriture quelconque sur le document. Cela définit le champ `_ts` selon l’heure actuelle. Le compte à rebours jusqu’à l’expiration du document, tel que défini par la `ttl`, recommence. Si vous souhaitez modifier la `ttl` d’un document, vous pouvez mettre à jour le champ comme n’importe quel autre champ définissable.

    response = await client.ReadDocumentAsync(
        "/dbs/salesdb/colls/orders/docs/SO05"), 
        new RequestOptions { PartitionKey = new PartitionKey("CO18009186470") });
    
    Document readDocument = response.Resource;
    readDocument.TimeToLive = 60 * 30 * 30; // update time to live
    
    response = await client.ReplaceDocumentAsync(salesOrder);

## <a name="removing-ttl-from-a-document"></a>Suppression de la TTL d’un document
Si une TTL a été définie sur un document et que vous ne souhaitez plus que ce document expire, vous pouvez extraire le document, supprimer le champ TTL et replacer le document sur le serveur. Lorsque le champ TTL est supprimé du document, la valeur par défaut de la collection est appliquée. Pour éviter qu’un document n’expire et n’hérite de la collection, vous devez définir la valeur de TTL sur -1.

    response = await client.ReadDocumentAsync(
        "/dbs/salesdb/colls/orders/docs/SO05"), 
        new RequestOptions { PartitionKey = new PartitionKey("CO18009186470") });
    
    Document readDocument = response.Resource;
    readDocument.TimeToLive = null; // inherit the default TTL of the collection
    
    response = await client.ReplaceDocumentAsync(salesOrder);

## <a name="disabling-ttl"></a>Désactivation de la TTL
Pour désactiver la TTL entièrement sur une collection et arrêter la recherche de documents expirés par le processus en arrière-plan, vous devez supprimer la propriété DefaultTTL de la collection. Supprimer cette propriété ne revient pas à la définir sur -1. Si vous la définissez sur -1, les nouveaux documents ajoutés à la collection auront une durée de vie infinie. Cependant, vous pouvez la remplacer sur des documents spécifiques de la collection. Si vous supprimez cette propriété de la collection entièrement, aucun document n’expirera, même si une valeur par défaut précédente a été explicitement remplacée sur certains documents.

    DocumentCollection collection = await client.ReadDocumentCollectionAsync("/dbs/salesdb/colls/orders");
    
    // Disable TTL
    collection.DefaultTimeToLive = null;
    
    await client.ReplaceDocumentCollectionAsync(collection);

<a id="ttl-and-index-interaction"></a> 
## <a name="ttl-and-index-interaction"></a>Interaction entre la TTL et l’index
L’ajout ou la modification du paramètre TTL sur une collection modifie l’index sous-jacent. Lorsque la valeur TTL passe de Désactivée à Activée, la collection est réindexée. Si vous apportez des modifications à la stratégie d’indexation et que le mode d’indexation est cohérent, les utilisateurs ne remarqueront aucune modification de l’index. Lorsque le mode d’indexation est différé, l’index est toujours en retard : si la valeur TTL change, l’index est recréé de A à Z. Lorsque la valeur TTL est modifiée et que le mode d’indexation est différé, les requêtes effectuées pendant la reconstruction de l’index ne retournent pas de résultats complets ou corrects.

Si vous avez besoin de données exactes, ne modifiez pas la valeur TTL avec un mode d’indexation différé. L’idéal est de choisir des index cohérents pour garantir des résultats de requête cohérents. 

## <a name="faq"></a>Forum Aux Questions
**Quel est le coût de la TTL ?**

La définition d’une TTL sur un document n’entraîne aucun coût supplémentaire.

**Combien de temps faut-il pour supprimer un document une fois la TTL activée ?**

Les documents expirent immédiatement quand la durée de vie est activée, et ils ne sont pas accessibles par le biais des opérations CRUD ou des API de requête. 

**La TTL d’un document a-t-elle un impact sur les frais d’unités de requête ?**

Non, il n’y a aucun impact sur les frais d’unités de requête pour les suppressions de documents ayant expiré par le biais de la durée de vie dans Cosmos DB.

**La fonctionnalité TTL s’applique-t-elle uniquement à des documents entiers, ou puis-je faire expirer des valeurs de propriété de document individuelles ?**

La TTL s’applique à l’ensemble du document. Si vous souhaitez faire expirer uniquement une partie d’un document, il est recommandé d’extraire la partie du document principal dans un document distinct « lié », puis d’utiliser la durée de vie sur ce document extrait.

**La fonction TTL impose-t-elle des exigences d’indexation spécifiques ?**

Oui. La collection doit avoir une [stratégie d’indexation](indexing-policies.md) définie sur Différée ou Cohérente. Une erreur se produira si vous tentez de définir le paramètre DefaultTTL sur une collection dont l’indexation est définie sur Aucune et si vous essayez de désactiver l’indexation sur une collection dont le paramètre DefaultTTL est déjà défini.

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur Azure Cosmos DB, consultez la page de [*documentation*](https://azure.microsoft.com/documentation/services/cosmos-db/) du service.

