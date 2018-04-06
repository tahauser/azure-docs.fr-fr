---
title: Introduction à l’API d’Azure Cosmos DB Cassandra | Microsoft Docs
description: Découvrez comment vous pouvez utiliser Azure Cosmos DB pour développer et transférer vos applications existantes et créer de nouvelles applications à l’aide de l’API Cassandra avec les pilotes Cassandra et CQL que vous connaissez déjà.
services: cosmos-db
author: govindk
manager: jhubbard
documentationcenter: ''
ms.assetid: 73839abf-5af5-4ae0-a852-0f4159bc00a0
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/20/2017
ms.author: govindk
ms.openlocfilehash: 88364cecc1fa1ad7318cb28c9708a42e6a807347
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2018
---
# <a name="introduction-to-azure-cosmos-db-apache-cassandra-api"></a>Présentation d’Azure Cosmos DB : API Apache Cassandra

Azure Cosmos DB fournit l’API Cassandra (préversion) aux applications qui sont écrites pour Apache Cassandra et qui ont besoin de fonctionnalités Premium comme :

* [Taille de stockage et débit évolutifs](partition-data.md).
* [Distribution mondiale clé en main](distribute-data-globally.md)
* Des latences de quelques millisecondes au 99e centile.
* [Cinq niveaux de cohérence bien définis](consistency-levels.md)
* [L’indexation automatique des données](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) sans avoir à s’occuper de la gestion des schémas et des index. 
* Garantie de haute disponibilité, soutenue par des [Contrats de niveau de service de pointe](https://azure.microsoft.com/support/legal/sla/cosmos-db/)

## <a name="what-is-the-azure-cosmos-db-apache-cassandra-api"></a>Qu’est l’API Azure Cosmos DB Apache Cassandra ?

Azure Cosmos DB peut être utilisé comme magasin de données pour les applications écrites pour [Apache Cassandra](https://cassandra.apache.org/), à l’aide de l’API Apache Cassandra. Cela signifie qu’à l’aide des [pilotes compatibles avec CQLv4 sous licence Apache](https://cassandra.apache.org/doc/latest/getting_started/drivers.html?highlight=driver) existants, votre application écrite pour Cassandra peut désormais communiquer avec l’API Azure Cosmos DB Cassandra. Dans de nombreux cas, vous pouvez basculer de l’utilisation d’Apache Cassandra à l’API Apache Cassandra de Azure Cosmos DB en modifiant simplement une chaîne de connexion. Cette fonctionnalité vous permet de générer et d’exécuter facilement des applications de base de données de l’API Cassandra dans le cloud Azure avec la distribution mondiale et les [contrats SLA de pointe](https://azure.microsoft.com/support/legal/sla/cosmos-db) d’Azure Cosmos DB, tout en continuant à exploiter leurs outils et compétences existants pour l’API Cassandra.

![API Cassandra Azure Cosmos DB](./media/cassandra-introduction/cosmosdb-cassandra.png)

L’API Cassandra vous permet d’interagir avec les données stockées dans la base de données Azure Cosmos à l’aide d’outils de langage de requête basés sur Cassandra (par exemple, CQLSH) et des pilotes client Cassandra que vous connaissez déjà. En savoir plus dans cette vidéo Microsoft Mechanics avec Kirill Gavrylyuk, responsable principal de l’ingénierie.

> [!VIDEO https://www.youtube.com/embed/1Sf4McGN1AQ]
>

## <a name="what-is-the-benefit-of-using-apache-cassandra-api-for-azure-cosmos-db"></a>Quel est l’avantage de l’utilisation de l’API Apache Cassandra pour Azure Cosmos DB ?

**Aucune gestion des opérations** : en tant que pur service entièrement géré, Azure Cosmos DB garantit que les administrateurs de l’API Cassandra n’ont pas à se soucier de la gestion et de la surveillance d’une multitude de paramètres entre les fichiers du système d’exploitation, JVM et yaml, ou de leur interaction. Cosmos Azure DB fournit une analyse du débit, de la latence, du stockage et de la disponibilité ainsi que des alertes configurables. 

**Gestion des performances** : Azure Cosmos DB fournit un contrat SLA soutenu par des lectures et écritures à faible latence garanties au 99e centile. Les utilisateurs n’ont pas besoin de se soucier de la surcharge opérationnelle pour fournir de bons contrats SLA de lecture et écriture. Ceux-ci incluent généralement la planification de compactage, la gestion des éléments résiduels, le réglage des filtres et les retards de réplication. Azure Cosmos DB vous retire immédiatement le problème de la gestion de ces problèmes et vous permet de vous concentrer sur les résultats de l’application.

**Indexation automatique** : Azure Cosmos DB indexe automatiquement toutes les colonnes de table dans la base de données de l’API Cassandra. Azure Cosmos DB ne nécessite pas la création d’index secondaires pour accélérer les requêtes. Il fournit une latence faible en lecture et en écriture tout en proposant une indexation automatique cohérente. 

**Possibilité d’utiliser du code et des outils** : Azure Cosmos DB fournit une compatibilité de niveau protocole câble avec les outils et kits de développement logiciel existants. Cette compatibilité garantit que vous pouvez utiliser votre base de code avec l’API Cassandra de Azure Cosmos DB avec des modifications triviales.

**Élasticité du débit et du stockage** : La plateforme Azure Cosmos offre une élasticité de débit garantie entre les régions via le portail simple, PowerShell ou des opérations de l’interface CLI. Vous pouvez mettre à l’échelle les tables Azure Cosmos DB de manière flexible et transparente avec des performances prévisibles à mesure que votre application se développe. Azure Cosmos DB prend en charge les tables de l’API Cassandra qui peuvent être mises à l’échelle pour atteindre des tailles de stockage quasi illimitées. 

**Disponibilité et distribution globale** : Azure Cosmos DB offre la possibilité de distribuer les données dans toutes les régions Azure pour offrir aux utilisateurs une expérience d’une latence faible tout en garantissant la disponibilité. Azure Cosmos DB offre une disponibilité de 99,99 % dans une région et 99,999 % de disponibilité en lecture à travers les régions avec aucune surcharge d’opérateur. Azure Cosmos DB est disponible dans plus de 30 [régions Azure](https://azure.microsoft.com/regions/services/). Pour en savoir plus, consultez [Distribution mondiale des données avec DocumentDB](distribute-data-globally.md). 

**Choix de la cohérence** : Azure Cosmos DB vous permet de choisir parmi cinq niveaux de cohérence bien définis pour obtenir un équilibre optimal entre cohérence et performances. Ces niveaux de cohérence sont : Forte, Obsolescence limitée, Session, Préfixe cohérent et Éventuelle. Ces niveaux de cohérence bien définis et granulaires permettent au développeur de trouver un bon compromis entre cohérence, disponibilité et latence. Pour en savoir plus, consultez [Niveaux de cohérence des données analysables dans Azure Cosmos DB](consistency-levels.md). 

**Niveau entreprise**: Sécurisé et conforme par défaut – Azure Cosmos DB fournit des [certifications de conformité](https://www.microsoft.com/trustcenter) pour garantir aux utilisateurs de pouvoir utiliser la plateforme sans se préoccuper des problèmes de conformité. Azure Cosmos DB fournit également le chiffrement au repos et en mouvement, un pare-feu IP et des journaux d’audit pour les activités de plan de contrôle.  

<a id="sign-up-now"></a>
## <a name="sign-up-now"></a>ici 

Si vous avez déjà un abonnement Azure, vous pouvez vous inscrire pour participer au programme API Cassandra (préversion) dans le [portail Azure](https://aka.ms/cosmosdb-cassandra-signup).  Si vous débutez avec Azure, inscrivez-vous pour un [essai gratuit](https://azure.microsoft.com/free) qui vous donnera 12 mois d’accès à Azure Cosmos DB. Procédez comme suit pour demander l’accès au programme API Cassandra (préversion).

1. Dans le [portail Azure](https://portal.azure.com), cliquez sur **Créer une ressource** > **Bases de données** > **Azure Cosmos DB**. 

2. Dans la page Nouveau compte, sélectionnez **Cassandra** dans la zone API. 

3. Dans la zone **Abonnement**, sélectionnez l’abonnement Azure que vous voulez utiliser pour ce compte.

4. Cliquez sur **Inscrivez-vous pour une préversion aujourd’hui**.

    ![API Cassandra Azure Cosmos DB](./media/cassandra-introduction/cassandra-sign-up.png)

3. Dans la fenêtre Inscrivez-vous pour une préversion aujourd’hui, cliquez sur **OK**. 

    Une fois que vous envoyez la demande, l’état passe à **En attente d’approbation** dans le volet Nouveau compte. 

Une fois que vous soumettez votre demande, attendez de recevoir par e-mail la notification indiquant que votre demande a été approuvée. En raison du volume élevé de demandes, vous devez recevoir une notification dans un délai d’une semaine. Vous n’avez pas besoin de créer un ticket de support pour effectuer la demande. Les demandes sont étudiées dans l’ordre dans lequel elles sont reçues. 

## <a name="how-to-get-started"></a>Pour commencer
Une fois que vous avez rejoint le programme en préversion, suivez les démarrages rapides pour l’API Cassandra afin de créer une application à l’aide de celle-ci :

* [Démarrage rapide : Créer une application web Cassandra avec Node.js et Azure Cosmos DB](create-cassandra-nodejs.md)
* [Démarrage rapide : Créer une application web Cassandra avec Java et Azure Cosmos DB](create-cassandra-java.md)
* [Démarrage rapide : Créer une application web Cassandra avec .NET et Azure Cosmos DB](create-cassandra-dotnet.md)
* [Démarrage rapide : Créer une application web Cassandra avec Python et Azure Cosmos DB](create-cassandra-python.md)

## <a name="next-steps"></a>Étapes suivantes

Des informations sur l’API Cassandra d’Azure Cosmos DB sont intégrées dans l’ensemble de la documentation Azure Cosmos DB, mais voici quelques points qui vous aideront à démarrer :

* Suivez les [Démarrages rapides](create-cassandra-nodejs.md) pour créer un compte et une nouvelle application à l’aide d’un exemple Git
* Suivez les [Didacticiels](tutorial-develop-cassandra-java.md) pour créer une nouvelle application par programme.
* Suivez le [Didacticiel sur l’importation de données Cassandra](cassandra-import-data.md) pour importer vos données existantes dans Azure Cosmos DB.
* Consultez le [Forum Aux Questions](faq.md#cassandra).
