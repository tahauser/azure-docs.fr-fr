---
title: 'Introduction à Azure Cosmos DB : API MongoDB | Microsoft Docs'
description: Découvrez comment vous pouvez utiliser Azure Cosmos DB pour stocker et interroger d’immenses volumes de requêtes de documents JSON avec une faible latence, à l’aide des API MongoDB OSS populaires.
keywords: qu’est-ce que MongoDB
services: cosmos-db
author: AndrewHoh
manager: jhubbard
editor: ''
documentationcenter: ''
ms.assetid: 4afaf40d-c560-42e0-83b4-a64d94671f0a
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2018
ms.author: anhoh
ms.openlocfilehash: cd80f349f2fb376178e02d947db1fefbcbe3ac88
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2018
---
# <a name="introduction-to-azure-cosmos-db-mongodb-api"></a>Introduction à Azure Cosmos DB : API MongoDB

[Azure Cosmos DB](../cosmos-db/introduction.md) est le service de base de données multi-modèle de Microsoft distribué à l’échelle mondiale pour les applications stratégiques. Azure Cosmos DB fournit la [distribution mondiale clés en main](distribute-data-globally.md), la [mise à l’échelle élastique du débit et du stockage](partition-data.md), des latences de l’ordre de quelques millisecondes dans le monde entier dans plus de 99 pour cent des cas, et une haute disponibilité garantie, le tout soutenu par nos [contrats de niveau de service de pointe](https://azure.microsoft.com/support/legal/sla/cosmos-db/). Azure Cosmos DB [indexe automatiquement les données](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) sans avoir à s’occuper de la gestion des schémas et des index. Il est multi-modèle et prend en charge les modèles de données en colonnes, documents, graphes et clé-valeur. 

![Azure Cosmos DB : API MongoDB](./media/mongodb-introduction/cosmosdb-mongodb.png) 

Les bases de données Azure Cosmos DB peuvent être utilisées comme magasin de données pour les applications écrites pour [MongoDB](https://docs.mongodb.com/manual/introduction/). Cette fonctionnalité signifie qu’en utilisant les [pilotes](https://docs.mongodb.org/ecosystem/drivers/) existants, votre application écrite pour MongoDB peut désormais communiquer avec Azure Cosmos DB et utiliser des bases de données Azure Cosmos DB au lieu de bases de données MongoDB. Dans de nombreux cas, vous pouvez passer de MongoDB à Azure Cosmos DB en modifiant simplement une chaîne de connexion. Cette fonctionnalité permet de générer et d’exécuter facilement des applications de base de données MongoDB distribuées à l’échelle mondiale dans le cloud Azure avec Azure Cosmos DB et ses [contrats de niveau de service complets et inégalés](https://azure.microsoft.com/support/legal/sla/cosmos-db), tout en continuant à exploiter les outils et les compétences existants pour MongoDB.

**Compatibilité de MongoDB** : vous pouvez utiliser votre expertise, votre code d’application et vos outils MongoDB existants, pendant qu’Azure Cosmos DB implémente le protocole Wire MongoDB 3.4 (version 5) et prend en charge le [pipeline d’agrégation MongoDB](mongodb-feature-support.md#aggregation-pipeline). Vous pouvez développer des applications à l’aide de MongoDB et les déployer en production avec le service Azure Cosmos DB entièrement géré et distribué à l’échelle mondiale.

## <a name="what-is-the-benefit-of-using-azure-cosmos-db-for-mongodb-applications"></a>Quel avantage pouvez-vous tirer en utilisant Azure Cosmos DB pour les applications MongoDB ?

**Stockage et débit extensibles de façon élastique :** répondez aux besoins de votre application en augmentant ou en réduisant facilement la taille de votre base de données MongoDB. Vos données sont stockées sur des disques SSD pour garantir une faible latence de manière prévisible. Azure Cosmos DB prend en charge des collections MongoDB qui peuvent être mises à l’échelle pour atteindre des tailles de stockage quasi illimitées et un débit configuré. Vous pouvez mettre à l’échelle Azure Cosmos DB de manière flexible et transparente avec des performances prévisibles à mesure que votre application se développe. 

**Réplication dans plusieurs régions :** Azure Cosmos DB réplique en toute transparence vos données dans toutes les régions associées à votre compte MongoDB, ce qui vous permet de développer des applications qui requièrent l’accès global aux données tout en offrant un compromis entre cohérence, disponibilité et performances, le tout avec les garanties correspondantes. Azure Cosmos DB fournit un basculement régional transparent avec les API multihébergement et la possibilité de mettre à l’échelle de manière élastique le débit et le stockage dans le monde entier. Pour en savoir plus, consultez [Distribution mondiale des données avec DocumentDB](distribute-data-globally.md).

**Aucune gestion de serveur** : vous n’avez pas à vous soucier de la gestion et de la mise à l’échelle de vos bases de données MongoDB. Azure Cosmos DB est un service entièrement géré, ce qui signifie que vous n’avez pas d’infrastructure ou de machines virtuelles à gérer. Azure Cosmos DB est disponible dans plus de 30 [régions Azure](https://azure.microsoft.com/regions/services/).

**Niveaux cohérence réglables** : Azure Cosmos DB implémente actuellement MongoDB version 3.4, qui a deux paramètres de cohérence, forte et éventuelle. Azure Cosmos DB utilisant plusieurs API, les paramètres de cohérence sont applicables au niveau du compte, et l’application de la cohérence est contrôlée par chaque API. Jusqu’à MongoDB 3.6, il n’y avait aucun concept de cohérence de session. Ainsi, si vous configurez un compte d’API MongoDB pour utiliser la cohérence de session, la cohérence est rétrogradée à éventuelle lorsque vous utilisez des API MongoDB. Si vous avez besoin d’une garantie de lecture de votre propre écriture pour un compte d’API MongoDB, le niveau de cohérence par défaut pour le compte doit être défini sur obsolescence forte ou limitée. Pour en savoir plus, consultez [Niveaux de cohérence des données analysables dans Azure Cosmos DB](consistency-levels.md).

| Niveau de cohérence par défaut d’Azure Cosmos DB |   API mongo (3.4) |
|---|---|
|Eventual (Éventuel)| Eventual (Éventuel) |
|Préfixe cohérent| Éventuelle avec ordre cohérent |
|session| Éventuelle avec ordre cohérent |
|Obsolescence limitée| Remarque |
| Remarque | Remarque |

**Indexation automatique :** par défaut, Azure Cosmos DB indexe automatiquement toutes les propriétés des documents de votre base de données MongoDB et n’attend pas et ne nécessite aucun schéma ou création d’index secondaires. En outre, la fonctionnalité d’index unique permet une contrainte d’unicité sur tous les champs de documents qui sont déjà indexés automatiquement dans Azure Cosmos DB.

**Qualité professionnelle** : Azure Cosmos DB prend en charge plusieurs réplicas locaux pour fournir une disponibilité de 99,99 % et une protection des données en cas de défaillances locales et régionales. Azure Cosmos DB offre des [certifications de conformité](https://www.microsoft.com/trustcenter) et des fonctionnalités de sécurité de qualité professionnelle. 

Pour en savoir plus, regardez cette vidéo avec le Senior Program Manager d’Azure Cosmos DB, Aleksey Savateyev.

> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T136/player]
> 

## <a name="how-to-get-started"></a>Pour commencer

Suivez les démarrages rapides de MongoDB pour créer un compte Azure Cosmos DB et migrer votre application Mongo DB existante afin d’utiliser Azure Cosmos DB ou d’en générer une nouvelle :

* [Migrer une application web MongoDB Node.js existante](create-mongodb-nodejs.md)
* [Développer une application web API MongoDB avec .NET et le Portail Azure](create-mongodb-dotnet.md)
* [Développer une application console API MongoDB avec Java et le Portail Azure](create-mongodb-java.md)

## <a name="next-steps"></a>Étapes suivantes

Des informations sur l’API MongoDB d’Azure Cosmos DB sont intégrées dans l’ensemble de la documentation Azure Cosmos DB, mais voici quelques conseils pour bien démarrer :

* Pour savoir comment obtenir les informations de chaîne de connexion de votre compte, suivez le didacticiel [Se connecter à un compte MongoDB](connect-mongodb-account.md).
* Pour savoir comment créer une connexion entre votre base de données Azure Cosmos DB et l’application MongoDB dans Studio 3T, suivez le didacticiel [Utiliser Studio 3T (MongoChef) avec Azure Cosmos DB](mongodb-mongochef.md).
* Suivez le didacticiel [Migrer des données vers Azure Cosmos DB à l’aide de mongoimport et mongorestore](mongodb-migrate.md) pour importer vos données dans une API de base de données MongoDB.
* Établissez une connexion à une API de compte MongoDB à l’aide de [Robomongo](mongodb-robomongo.md).
* Découvrez combien d’unités de requête sont utilisées par vos opérations avec la commande [GetLastRequestStatistics et des mesures du portail Azure](request-units.md#GetLastRequestStatistics).
* Découvrez comment [configurer des préférences de lecture pour les applications distribuées dans le monde entier](../cosmos-db/tutorial-global-distribution-mongodb.md).
