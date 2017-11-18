---
title: "Introduction à Azure Cosmos DB | Microsoft Docs"
description: "En savoir plus sur Azure Cosmos DB. Cette base de données multimodèle distribuée à l’échelle mondiale est conçue pour offrir une faible latence, une scalabilité élastique et une haute disponibilité."
services: cosmos-db
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: a855183f-34d4-49cc-9609-1478e465c3b7
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 07/14/2017
ms.author: mimig
ms.custom: mvc
ms.openlocfilehash: d5ae2a68f749310d4d6a2514055fb258978eec9c
ms.sourcegitcommit: d03907a25fb7f22bec6a33c9c91b877897e96197
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/12/2017
---
# <a name="welcome-to-azure-cosmos-db"></a>Bienvenue dans Azure Cosmos DB

Azure Cosmos DB est un service de base de données multimodèle mondialement distribué de Microsoft. En un clic, le service Azure Cosmos DB vous permet de faire évoluer en toute flexibilité et de façon indépendante le débit et le stockage sur n’importe quel nombre de régions géographiques Azure. Il offre des garanties en termes de débit, de latence, de disponibilité et de cohérence avec des [contrats SLA complets](https://aka.ms/acdbsla), ce qu’aucun autre service de base de données ne peut offrir. Vous pouvez [essayer Azure Cosmos DB gratuitement](https://azure.microsoft.com/try/cosmosdb/) sans abonnement Azure, libre de tous frais et engagements.

![Azure Cosmos DB est le service de base de données distribué mondialement de Microsoft qui propose une augmentation de la taille des instances, une faible latence, cinq modèles de cohérence et des contrats SLA offrant des garanties complètes](./media/introduction/azure-cosmos-db.png)

> [!div class="nextstepaction"]
> [Essayez gratuitement Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/)

## <a name="key-capabilities"></a>Fonctionnalités clés
En tant que service de base de données distribué mondialement, Azure Cosmos DB fournit les fonctionnalités suivantes pour vous aider à créer des applications évolutives et très réactives :

* **Une distribution mondiale clé en main**
    * Vous pouvez [distribuer vos données](distribute-data-globally.md) à un nombre illimité de [régions Azure](https://azure.microsoft.com/regions/), d’un [simple clic](tutorial-global-distribution-documentdb.md). Cela vous permet de placer vos données là où se trouvent vos utilisateurs, assurant ainsi la latence la plus faible possible pour vos clients. 
    * À l’aide des API multihébergement d’Azure Cosmos DB, l’application sait où se trouve la région la plus proche et envoie les requêtes au centre de données le plus proche. Tout ceci est possible sans aucune modification de configuration. Vous définissez votre région d’écriture et autant de régions de lecture que vous souhaitez, le reste est géré pour vous.

* **Plusieurs modèles de données et API populaires pour la consultation et l’interrogation des données**
    * Le modèle de données basé sur le atome-enregistrement-séquence (ARS) sur lequel repose Azure Cosmos DB prend en charge en mode natif plusieurs modèles de données, incluant notamment les documents, les graphes, les clés-valeurs, les tables et les modèles de données en colonnes.
    * Les API pour les modèles suivants sont pris en charge avec les SDK et sont disponibles dans plusieurs langages :
        * [API DocumentDB](documentdb-introduction.md) : moteur de base de données JSON sans schéma avec des fonctions d’interrogation SQL.
        * [API MongoDB](mongodb-introduction.md) : service de base de données MongoDB reposant sur Cosmos DB. Compatible avec les bibliothèques, pilotes, outils et applications MongoDB existants.
        * [API Table](table-introduction.md) : service de base de données de stockage de clés-valeurs qui fournit des fonctionnalités haut de gamme pour les applications de stockage Table Azure.
        * [API Graph (Gremlin)](graph-introduction.md) : service de base de données graphique conçu conformément à la [spécification Apache TinkerPop](http://tinkerpop.apache.org/).
        * [API Cassandra](cassandra-introduction.md) : service de base de données orienté colonne. Compatible avec les bibliothèques, pilotes, outils et applications Cassandra existants.
        * Des modèles de données supplémentaires seront bientôt disponibles.

* **Ajuster le débit et le stockage de façon élastique et à la demande, dans le monde entier**
    * Faites évoluer facilement le débit de la base de données avec une granularité [par seconde](request-units.md) et modifiez-le à tout moment. 
    * Mettez à l’échelle la taille de stockage de façon [automatique et transparente](partition-data.md) pour gérer vos besoins existants et futurs.

* **Créer des applications hautement réactives et stratégiques**
    * Azure Cosmos DB garantit une faible latence de bout en bout au 99e centile à ses clients. 
    * Pour un document de 1 Ko, Cosmos DB garantit une latence de bout en bout des lectures sous 10 ms et des écritures indexées sous 15 ms au 99e centile, dans la même région Azure. Les latences médianes sont nettement plus faibles (inférieures à 5 ms).

* **Assurer une disponibilité en continu**
    * Disponibilité de 99,99 % dans une même région.
    * Déployez dans n’importe quel nombre de [régions Azure](https://azure.microsoft.com/regions) pour bénéficier d’une haute disponibilité.
    * [Simulez la défaillance](regional-failover.md) d’une ou plusieurs régions avec comme garantie l’absence de perte de données. 

* **Écrire des applications distribuées mondialement, de la bonne façon**
    * Les cinq [modèles de cohérence](consistency-levels.md) fournissent un spectre de cohérence allant d’une cohérence forte de type SQL jusqu’à une cohérence éventuelle de type NoSQL, comprenant toutes les cohérences intermédiaires. 
  
* **Garantie de remboursement**
    * Vos données sont accessibles rapidement ou vous êtes remboursé. 
    * [Contrats SLA](https://aka.ms/acdbsla) pour la disponibilité, la latence, le débit et la cohérence. 

* **Aucune gestion de schéma ou d’index de base de données**
    * Vous n'avez plus à conserver le schéma et les index de base de données synchronisés avec le schéma de l’application. Les schémas ne sont plus nécessaires. 
    * Le moteur de base de données Azure Cosmos DB est entièrement indépendant du schéma : il indexe automatiquement toutes les données qu’il reçoit sans schéma ni index et répond aux requêtes de façon ultrarapide. 

* **Faible coût total de possession**
    * Cinq à dix fois [plus économique](https://aka.ms/cosmos-db-tco-paper) qu’une solution non managée.
    * Prix trois fois moins élevé que DynamoDB.

## <a name="capability-comparison"></a>Comparaison des fonctionnalités

Azure Cosmos DB fournit les meilleures fonctionnalités des bases de données relationnelles et non relationnelles.

| Fonctionnalités | Bases de données relationnelles   | Bases de données non relationnelles (NoSQL) |    Azure Cosmos DB |
| --- | --- | --- | --- |
| Diffusion mondiale | Non | Non | Oui, la distribution clé en main est disponible avec les API multihébergement dans plus de 30 régions.|
| Mise à l’échelle horizontale | Non | Oui | Oui, vous pouvez ajuster le stockage et le débit indépendamment. | 
| Garanties de latence | Non | Oui | Oui, la latence de 99 % des lectures est inférieure à 10 ms et celle des écritures est inférieure à 15 ms. | 
| Haute disponibilité | Non | Oui | Oui, Cosmos DB est toujours disponible, respecte les compromis PACELC, et permet des basculements manuels et automatiques.|
| Modèle de données + API | Relationnel + SQL | Multi-modèle + API OSS | Multi-modèle + SQL + API OSS (autres fonctionnalités disponibles prochainement) |
| Contrats SLA | Oui | Non | Oui, les contrats SLA sont complets en termes de latence, de débit, de cohérence et de disponibilité. |

## <a name="solutions-that-benefit-from-azure-cosmos-db"></a>Les solutions qui profitent des avantages de Azure Cosmos DB

N’importe quelle [application web, mobile, IoT ou de jeux](use-cases.md) devant gérer des volumes importants de lectures et d’écritures sur une échelle [globale](distribute-data-globally.md) avec un faible temps de réponse pour une grande variété de données profite de la disponibilité, du débit élevé, de la faible latence et de la cohérence ajustable [garanties](https://azure.microsoft.com/support/legal/sla/cosmos-db/) d’Azure Cosmos DB. Découvrez comment CosmosDB peut être appliqué à [IoT et la télématique](use-cases.md#iot-and-telematics), à la [vente au détail et au marketing](use-cases.md#retail-and-marketing), aux [jeux](use-cases.md#gaming) et aux [applications mobiles et web](use-cases.md#web-and-mobile-applications) .

## <a name="next-steps"></a>Étapes suivantes
Bien démarrer avec Azure Cosmos DB grâce à l’un de nos guides de démarrage rapide :

* [Bien démarrer avec l’API DocumentDB d’Azure Cosmos DB](create-documentdb-dotnet.md)
* [Bien démarrer avec l’API MongoDB d’Azure Cosmos DB](create-mongodb-nodejs.md)
* [Bien démarrer avec l’API Graph d’Azure Cosmos DB](create-graph-dotnet.md)
* [Bien démarrer avec l’API Table d’Azure Cosmos DB](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [Essayez gratuitement Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/)
