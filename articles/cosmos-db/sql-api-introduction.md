---
title: 'Azure Cosmos DB : Présentation de l’API SQL | Microsoft Docs'
description: Découvrez comment vous pouvez utiliser Azure Cosmos DB pour stocker et interroger d’immenses volumes de données de documents JSON avec une faible latence, à l’aide de SQL et JavaScript.
keywords: base de données JSON, base de données Document
services: cosmos-db
author: rafats
manager: jhubbard
editor: monicar
documentationcenter: ''
ms.assetid: 686cdd2b-704a-4488-921e-8eefb70d5c63
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/22/2017
ms.author: rafats
ms.openlocfilehash: 32313c28de9065b57ec98d3668813a4b24c0becc
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="introduction-to-azure-cosmos-db-sql-api"></a>Présentation d’Azure Cosmos DB : API SQL

[Azure Cosmos DB](introduction.md) est le service de base de données multi-modèle de Microsoft distribué à l’échelle mondiale pour les applications stratégiques. Azure Cosmos DB fournit la [distribution mondiale clés en main](distribute-data-globally.md), la [mise à l’échelle élastique du débit et du stockage](partition-data.md), des latences de l’ordre de quelques millisecondes dans le monde entier dans plus de 99 pour cent des cas, [cinq niveaux de cohérence bien définis](consistency-levels.md) et une garantie d’une haute disponibilité, le tout soutenu par nos [contrats SLA de pointe](https://azure.microsoft.com/support/legal/sla/cosmos-db/). Azure Cosmos DB [indexe automatiquement les données](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) sans avoir à s’occuper de la gestion des schémas et des index. Il est multi-modèle et prend en charge les modèles de données en colonnes, documents, graphes et clé-valeur.

![API SQL Azure](./media/sql-api-introduction/cosmosdb-sql-api.png) 

Avec l’API SQL, Azure Cosmos DB fournit des [fonctionnalités de requête SQL](sql-api-sql-query.md) enrichies et familières avec une faible latence sur les données JSON sans schéma. Dans cet article, nous proposons une vue d’ensemble de l’API SQL d’Azure Cosmos DB, et expliquons comment l’utiliser pour stocker d’immenses volumes de données JSON, les interroger avec une latence de quelques millisecondes, et faire évoluer facilement le schéma. 

## <a name="what-capabilities-and-key-features-does-azure-cosmos-db-offer"></a>Quelles fonctionnalités et caractéristiques clés sont offertes par Azure Cosmos DB ?
Azure Cosmos DB, via l’API SQL, offre les avantages et fonctionnalités clés suivants :

* **Débit et stockage évolutifs avec flexibilité :** augmentez ou réduisez facilement la taille de votre base de données JSON pour répondre aux besoins de votre application. Vos données sont stockées sur des disques SSD pour garantir une faible latence de manière prévisible. Azure Cosmos DB prend en charge les conteneurs pour le stockage des données JSON (appelées collections) qui peuvent atteindre des tailles de stockage quasi-illimitées associées à un débit approvisionné. Vous pouvez mettre à l’échelle Azure Cosmos DB de manière flexible et transparente avec des performances prévisibles à mesure que votre application se développe. 


* **Réplication dans plusieurs régions :** Azure Cosmos DB réplique en toute transparence vos données dans toutes les régions associées à votre compte Azure Cosmos DB, ce qui vous permet de développer des applications qui requièrent l’accès global aux données tout en offrant un compromis entre cohérence, disponibilité et performances, le tout avec les garanties correspondantes. Azure Cosmos DB fournit un basculement régional transparent avec les API multihébergement et la possibilité de mettre à l’échelle de manière élastique le débit et le stockage dans le monde entier. Pour en savoir plus, consultez [Azure Cosmos DB, un service de base de données mondialement distribué sur Azure](distribute-data-globally.md).

* **Requêtes ad hoc à l’aide de la syntaxe SQL familière :** stockez des documents JSON hétérogènes et interrogez-les via la syntaxe SQL. Azure Cosmos DB utilise une technologie d’indexation parallèle, structurée par des journaux et sans verrouillage qui permet d’indexer automatiquement tout le contenu des documents. Celle-ci autorise les requêtes enrichies en temps réel sans qu’il soit nécessaire de spécifier des indicateurs de schéma, des index secondaires ou des vues. Pour en savoir plus, consultez [Requête SQL et syntaxe SQL dans Azure Cosmos DB](sql-api-sql-query.md). 
* **Exécution de JavaScript dans la base de données :** exprimez la logique d’application en tant que procédures stockées, déclencheurs et fonctions définies par l’utilisateur à l’aide du code JavaScript standard. Cela permet à la logique d’application d’opérer via les données sans se soucier des incompatibilités entre l’application et le schéma de base de données. L’API SQL fournit l’exécution transactionnelle complète de la logique d’application JavaScript directement dans le moteur de base de données. Cette intégration approfondie de JavaScript permet l’exécution des opérations INSERT, REPLACE, DELETE et SELECT à partir d’un programme JavaScript en tant que transaction isolée. Pour plus d’informations, consultez la rubrique [Programmation Azure Cosmos DB côté serveur : procédures stockées, déclencheurs de base de données et fonctions définies par l’utilisateur](programming.md).

* **Niveaux de cohérence ajustables** : opérez un choix parmi cinq niveaux de cohérence bien définis pour obtenir un équilibre optimal entre cohérence et performances. Pour les requêtes et les opérations de lecture, Azure Cosmos DB propose cinq niveaux de cohérence distincts : Fort, En fonction de l’obsolescence, Par session, Préfixe cohérent et Éventuel. Ces niveaux de cohérence bien définis et granulaires vous permettent de trouver un bon compromis entre cohérence, disponibilité et latence. Pour en savoir plus, consultez [Niveaux de cohérence des données analysables dans Azure Cosmos DB](consistency-levels.md).

* **Gestion globale :** Ne vous souciez plus de gérer les ressources de base de données et d’ordinateur. Étant donné qu’il s’agit d’un service Microsoft Azure entièrement géré, vous n’avez pas à gérer de machines virtuelles, à déployer et configurer des logiciels, à gérer la mise à l’échelle ni à vous préoccuper des mises à niveau de la couche données. Chaque base de données est automatiquement sauvegardée et protégée contre les défaillances régionales. Vous pouvez facilement ajouter un compte Azure Cosmos DB et configurer la capacité dont vous avez besoin, et ainsi vous concentrer pleinement sur votre application plutôt que sur le fonctionnement et la gestion de votre base de données. 

* **Conception ouverte :** Démarrez rapidement à l’aide de compétences et d’outils existants. La programmation dans l’API SQL est simple, accessible et ne nécessite pas d’adopter de nouveaux outils ou d’adhérer à des extensions personnalisées JSON ou JavaScript. Vous pouvez accéder à toutes les fonctionnalités de base de données, y compris CRUD, les requêtes et le traitement JavaScript via une interface HTTP RESTful. L’API SQL englobe les formats, normes et langages existants, tout en offrant des fonctionnalités de base de données très précieuses.

* **Indexation automatique :** par défaut, Azure Cosmos DB indexe automatiquement tous les documents de la base de données et n’attend pas et ne nécessite aucun schéma ni création d’index secondaires. Vous ne voulez pas tout indexer ? Ne vous inquiétez pas, vous pouvez aussi [refuser des chemins d’accès dans vos fichiers JSON](indexing-policies.md) .

* **Support de flux de modification :** le flux de modification fournit une liste triée de documents au sein d’une collection Azure Cosmos DB affichés dans l’ordre dans lequel ils ont été modifiés. Ce flux peut être utilisé afin de détecter des modifications au niveau des données dans le but de répliquer les données, de déclencher des appels d’API ou d’exécuter un traitement de flux sur les mises à jour. Flux de modification est activé automatiquement et simple à utiliser : [en savoir plus sur le flux de modification](https://docs.microsoft.com/azure/cosmos-db/change-feed). 

## <a name="data-management"></a>Comment gérer les données avec l’API SQL ?
L’API SQL permet de gérer les données JSON via des ressources de base de données bien définies. Ces ressources sont répliquées à des fins de haute disponibilité et adressables de manière unique via leur URI logique. L’API SQL offre un modèle de programmation RESTful simple basé sur HTTP pour toutes les ressources. 


Un compte de base de données Azure Cosmos DB est un espace de noms unique qui vous donne accès à Azure Cosmos DB. Pour pouvoir créer un compte de base de données, vous devez posséder un abonnement Azure, lequel vous donne accès à une grande variété de services Azure. 

Toutes les ressources au sein d’Azure Cosmos DB sont modélisées et stockées en tant que documents JSON. Les ressources sont gérées en tant qu’éléments (documents JSON contenant des métadonnées) et en tant que flux (collections d’éléments). Les ensembles d'éléments sont contenus dans leurs flux respectifs.

L’image ci-dessous affiche les relations entre les ressources Azure Cosmos DB :

![Relation hiérarchique entre les ressources dans Azure Cosmos DB][1] 

Un compte de base de données est constitué d'un ensemble de bases de données. Chacune d'elles contient plusieurs collections, lesquelles comportent des procédures stockées, des déclencheurs, des fonctions définies par l'utilisateur, des documents et les pièces jointes associées. Une base de données est aussi associée à des utilisateurs, chacun possédant un ensemble d'autorisations permettant d'accéder à d'autres collections, procédures stockées, déclencheurs, fonctions définies par l'utilisateur, documents ou pièces jointes. Les bases de données, les utilisateurs, les autorisations et les collections sont des ressources définies par le système avec des schémas connus, tandis que les documents, les procédures stockées, les fonctions définies par l’utilisateur et les pièces jointes comportent un contenu JSON arbitraire défini par l’utilisateur.  

## <a name="develop"></a> Comment puis-je développer des applications avec l’API SQL ?

Azure Cosmos DB expose les ressources via l’API REST qui peut être appelée par n’importe quel langage capable de créer des requêtes HTTP/HTTPS. Par ailleurs, nous fournissons des bibliothèques de programmation pour plusieurs langages populaires pour l’API SQL. Les bibliothèques clientes simplifient de nombreuses tâches associées à l’API, car elles traitent les détails tels que la mise en cache d’adresses, la gestion des exceptions, les nouvelles tentatives automatiques, etc. Elles sont actuellement disponibles pour les langages et les plateformes suivants :  

| Download | Documentation |
| --- | --- |
| [KIT DE DÉVELOPPEMENT LOGICIEL (SDK) .NET](http://go.microsoft.com/fwlink/?LinkID=402989) |[Bibliothèque .NET](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet) |
| [Kit de développement logiciel (SDK) Node.js](http://go.microsoft.com/fwlink/?LinkID=402990) |[Bibliothèque Node.js](http://azure.github.io/azure-documentdb-node/) |
| [Kit de développement logiciel (SDK) Java](http://go.microsoft.com/fwlink/?LinkID=402380) |[Bibliothèque Java](/java/api/com.microsoft.azure.documentdb) |
| [Kit de développement logiciel (SDK) JavaScript](http://go.microsoft.com/fwlink/?LinkID=402991) |[Bibliothèque JavaScript](http://azure.github.io/azure-documentdb-js/) |
| n/a |[Kit de développement logiciel (SDK) JavaScript côté serveur](http://azure.github.io/azure-documentdb-js-server/) |
| [Kit de développement logiciel (SDK) Python](https://pypi.python.org/pypi/pydocumentdb) |[Bibliothèque Python](http://azure.github.io/azure-documentdb-python/) |
| n/a | [API pour MongoDB](mongodb-introduction.md)


[L’émulateur Azure Cosmos DB](local-emulator.md) vous permet de développer et tester votre application localement avec l’API SQL, sans créer d’abonnement Azure et sans frais. Lorsque vous êtes satisfait du fonctionnement de votre application dans l’émulateur, vous pouvez commencer à utiliser un compte Azure Cosmos DB dans le cloud.

En plus des opérations classiques de création, de lecture et de suppression, l’API SQL fournit une interface de requêtes SQL enrichies pour la récupération de documents JSON et une prise en charge côté serveur de l’exécution transactionnelle de la logique d’application JavaScript. Les interfaces d'exécution des requêtes et des scripts sont disponibles dans toutes les bibliothèques de plateforme, ainsi que les API REST. 

### <a name="sql-query"></a>Requête SQL
Azure Cosmos DB accepte l’interrogation de documents à l’aide d’un langage SQL prenant sa racine dans le système de type JavaScript, ainsi que les expressions prenant en charge les requêtes relationnelles, hiérarchiques et spatiales. Le langage de requête Azure Cosmos DB est une interface simple et performante qui permet d’interroger des documents JSON. Le langage prend en charge un sous-ensemble de syntaxe ANSI SQL et ajoute une intégration approfondie des objets JavaScript, des tableaux, de la construction d'objets et de l'appel de fonctions. L’API SQL fournit son modèle de requête sans que le développeur ait à fournir un schéma explicite ou des indicateurs d’indexation.

Les fonctions définies par l’utilisateur peuvent être inscrites auprès de l’API SQL et référencées dans le cadre d’une requête SQL, étendant ainsi la syntaxe pour prendre en charge une logique d’application personnalisée. Elles sont écrites en tant que programmes JavaScript et exécutées dans la base de données. 

Pour les développeurs .NET, le [kit de développement logiciel (SDK) .NET](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.aspx) de l’API SQL propose également un fournisseur de requête LINQ. 

### <a name="transactions-and-javascript-execution"></a>Transactions et exécution JavaScript
L’API SQL vous permet d’écrire la logique d’application sous forme de programmes nommés écrits entièrement en JavaScript. Ces programmes sont inscrits pour une collection et prennent en charge l'émission d'opérations de base de données sur les documents d'une collection donnée. JavaScript peut être inscrit à des fins d'exécution en tant que déclencheur, procédure stockée ou fonction définie par l'utilisateur. Les déclencheurs et les procédures stockées permettent de créer, lire, mettre à jour et supprimer des documents, tandis que les fonctions définies par l'utilisateur sont exécutées dans le cadre de la logique d'exécution de requêtes sans accès en écriture à la collection.

L'exécution JavaScript dans Cosmos DB est modélisée selon les concepts pris en charge par les systèmes de bases de données relationnelles, avec JavaScript en remplacement de Transact-SQL. Toute la logique JavaScript est exécutée dans une transaction ACID ambiante avec isolement de capture instantanée. Lors de son exécution, si le code JavaScript lève une exception, toute la transaction est abandonnée.

## <a name="next-steps"></a>Étapes suivantes
Vous disposez déjà d’un compte Azure ? Vous pouvez donc découvrir Azure Cosmos DB en suivant nos [démarrages rapides](../cosmos-db/create-sql-api-dotnet.md), qui vous guideront pour créer un compte et prendre en main Cosmos DB.

[1]: ./media/sql-api-introduction/json-database-resources1.png

