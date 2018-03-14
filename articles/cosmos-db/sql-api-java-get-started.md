---
title: 'Didacticiel NoSQL : API SQL pour le SDK Java Azure Cosmos DB | Microsoft Docs'
description: "Didacticiel NoSQL qui crée une base de données en ligne et une application console Java à l’aide de l’API SQL pour Azure Cosmos DB. Azure SQL est une base de données NoSQL pour JSON."
keywords: "didacticiel nosql, base de données en ligne, application console java"
services: cosmos-db
documentationcenter: Java
author: arramac
manager: jhubbard
editor: monicar
ms.assetid: 75a9efa1-7edd-4fed-9882-c0177274cbb2
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 05/22/2017
ms.author: arramac
ms.openlocfilehash: 9714234411e96074daae17b4711a52991768bd7b
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/18/2017
---
# <a name="nosql-tutorial-build-a-sql-api-java-console-application"></a>Didacticiel NoSQL : Créer une application console Java avec l’API SQL
> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [Node.js pour MongoDB](mongodb-samples.md)
> * [Node.JS](sql-api-nodejs-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [C++](sql-api-cpp-get-started.md)
>  
> 

[!INCLUDE [cosmos-db-sql-api](../../includes/cosmos-db-sql-api.md)]

Bienvenue dans le didacticiel NoSQL de l’API SQL pour le SDK Java Azure Cosmos DB ! À la fin de ce didacticiel, vous disposerez d’une application de console qui crée et interroge des ressources Azure Cosmos DB.

Le programme est le suivant :

* Création et connexion à un compte Azure Cosmos DB
* Configuration de votre solution Visual Studio
* Création d’une base de données en ligne
* Création d’une collection
* Création de documents JSON
* Interrogation de la collection
* Création de documents JSON
* Interrogation de la collection
* Remplacement d'un document
* Suppression d’un document
* Suppression de la base de données

Commençons dès maintenant !

## <a name="prerequisites"></a>Prérequis
Vérifiez que vous disposez des éléments suivants :

* Un compte Azure actif. Si vous n’en avez pas, vous pouvez vous inscrire pour bénéficier d’un [compte gratuit](https://azure.microsoft.com/free/). 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Git](https://git-scm.com/downloads).
* [Kit de développement logiciel Java (JDK) 7+](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Maven](http://maven.apache.org/download.cgi).

## <a name="step-1-create-an-azure-cosmos-db-account"></a>Étape 1 : créer un compte Azure Cosmos DB
Commençons par créer un compte Azure Cosmos DB. Si vous avez déjà un compte que vous souhaitez utiliser, vous pouvez passer directement à l’étape [Cloner le projet GitHub](#GitClone). Si vous utilisez l’émulateur Azure Cosmos DB, suivez les étapes de la section [Émulateur Azure Cosmos DB](local-emulator.md) pour configurer l’émulateur et passez directement à l’étape [Cloner le projet GitHub](#GitClone).

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="GitClone"></a>Étape 2 : cloner le projet GitHub
Vous pouvez commencer par cloner le référentiel GitHub pour une bonne [prise en main d’Azure Cosmos DB et de Java](https://github.com/Azure-Samples/documentdb-java-getting-started). Par exemple, à partir d’un répertoire local, exécutez la commande suivante pour récupérer l’exemple de projet localement.

    git clone git@github.com:Azure-Samples/azure-cosmos-db-documentdb-java-getting-started.git

    cd azure-cosmos-db-documentdb-java-getting-started

Le répertoire contient un fichier `pom.xml` pour le projet et un dossier `src` contenant le code source Java, y compris `Program.java` qui montre comment effectuer des opérations simples avec Azure Cosmos DB, comme créer des documents et interroger des données dans une collection. Le fichier `pom.xml` inclut une dépendance au [SDK Java d’Azure Cosmos DB sur Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb).

    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-documentdb</artifactId>
        <version>LATEST</version>
    </dependency>

## <a id="Connect"></a>Étape 3 : se connecter à un compte Azure Cosmos DB
Ensuite, revenez au [portail Azure](https://portal.azure.com) pour récupérer votre point de terminaison et votre clé primaire. Le point de terminaison et la clé primaire Azure Cosmos DB sont nécessaires pour que votre application sache où se connecter et qu’Azure Cosmos DB approuve la connexion de votre application.

Dans le portail Azure, accédez à votre compte Azure Cosmos DB, puis cliquez sur **Clés**. Copiez l’URI à partir du portail et collez-le dans `https://FILLME.documents.azure.com` dans le fichier Program.java. Ensuite, copiez la clé primaire à partir du portail, puis collez-la dans `FILLME`.

    this.client = new DocumentClient(
        "https://FILLME.documents.azure.com",
        "FILLME"
        , new ConnectionPolicy(),
        ConsistencyLevel.Session);

![Capture d’écran du portail Azure utilisé par le didacticiel NoSQL pour créer une application console Java. Présente un compte Azure Cosmos DB, avec le hub ACTIF et le bouton CLÉS mis en surbrillance dans le panneau du compte Azure Cosmos DB, et les valeurs d’URI, de CLÉ PRIMAIRE et de CLÉ SECONDAIRE mises en surbrillance dans le panneau Clés][keys]

## <a name="step-4-create-a-database"></a>Étape 4 : créer une base de données
Pour créer votre [base de données](sql-api-resources.md#databases) Azure Cosmos DB, utilisez la méthode [createDatabase](/java/api/com.microsoft.azure.documentdb._document_client.createdatabase) de la classe **DocumentClient**. Une base de données est le conteneur logique de stockage de documents JSON partitionné entre les collections.

    Database database = new Database();
    database.setId("familydb");
    this.client.createDatabase(database, null);

## <a id="CreateColl"></a>Étape 5 : créer une collection
> [!WARNING]
> **createCollection** crée une collection avec un débit réservé, ce qui a des conséquences sur la tarification. Pour plus d’informations, consultez notre [page de tarification](https://azure.microsoft.com/pricing/details/cosmos-db/).
> 
> 

Pour créer une [collection](sql-api-resources.md#collections), utilisez la méthode [createCollection](/java/api/com.microsoft.azure.documentdb._document_client.createcollection) de la classe **DocumentClient**. Une collection est un conteneur de documents JSON. Elle est associée à une logique d'application JavaScript.


    DocumentCollection collectionInfo = new DocumentCollection();
    collectionInfo.setId("familycoll");

    // Azure Cosmos DB collections can be reserved with throughput specified in request units/second. 
    // Here we create a collection with 400 RU/s.
    RequestOptions requestOptions = new RequestOptions();
    requestOptions.setOfferThroughput(400);

    this.client.createCollection("/dbs/familydb", collectionInfo, requestOptions);

## <a id="CreateDoc"></a>Étape 6 : Création de documents JSON
Pour créer un [document](sql-api-resources.md#documents), utilisez la méthode [createDocument](/java/api/com.microsoft.azure.documentdb._document_client.createdocument) de la classe **DocumentClient**. Les documents sont du contenu JSON (arbitraire) défini par l'utilisateur. Nous pouvons maintenant insérer un ou plusieurs documents. Si vous disposez déjà de données que vous souhaitez stocker dans votre base de données, vous pouvez utiliser [l’outil de migration de données](import-data.md) d’Azure Cosmos DB pour les importer dans une base de données.

    // Insert your Java objects as documents 
    Family andersenFamily = new Family();
    andersenFamily.setId("Andersen.1");
    andersenFamily.setLastName("Andersen")

    // More initialization skipped for brevity. You can have nested references
    andersenFamily.setParents(new Parent[] { parent1, parent2 });
    andersenFamily.setDistrict("WA5");
    Address address = new Address();
    address.setCity("Seattle");
    address.setCounty("King");
    address.setState("WA");

    andersenFamily.setAddress(address);
    andersenFamily.setRegistered(true);

    this.client.createDocument("/dbs/familydb/colls/familycoll", family, new RequestOptions(), true);

![Diagramme illustrant la relation hiérarchique existant entre le compte, la base de données en ligne, la collection et les documents utilisés par le didacticiel NoSQL pour créer une application console C#](./media/sql-api-get-started/nosql-tutorial-account-database.png)

## <a id="Query"></a>Étape 7 : interroger les ressources Azure Cosmos DB
Azure Cosmos DB prend en charge les [requêtes](sql-api-sql-query.md) enrichies sur les documents JSON stockés dans chaque collection.  L’exemple de code suivant montre comment interroger des documents dans Azure Cosmos DB à l’aide de la syntaxe SQL avec la méthode [queryDocuments](/java/api/com.microsoft.azure.documentdb._document_client.querydocuments).

    FeedResponse<Document> queryResults = this.client.queryDocuments(
        "/dbs/familydb/colls/familycoll",
        "SELECT * FROM Family WHERE Family.lastName = 'Andersen'", 
        null);

    System.out.println("Running SQL query...");
    for (Document family : queryResults.getQueryIterable()) {
        System.out.println(String.format("\tRead %s", family));
    }

## <a id="ReplaceDocument"></a>Étape 8 : remplacer le document JSON
Azure Cosmos DB prend en charge la mise à jour des documents JSON à l’aide de la méthode [replaceDocument](/java/api/com.microsoft.azure.documentdb._document_client.replacedocument).

    // Update a property
    andersenFamily.Children[0].Grade = 6;

    this.client.replaceDocument(
        "/dbs/familydb/colls/familycoll/docs/Andersen.1", 
        andersenFamily,
        null);

## <a id="DeleteDocument"></a>Étape 9 : supprimer le document JSON
De la même manière, Azure Cosmos DB prend en charge la suppression des documents JSON à l’aide de la méthode [deleteDocument](/java/api/com.microsoft.azure.documentdb._document_client.deletedocument).  

    this.client.delete("/dbs/familydb/colls/familycoll/docs/Andersen.1", null);

## <a id="DeleteDatabase"></a>Étape 10 : supprimer la base de données
Supprimer la base de données créée revient à supprimer la base de données et toutes les ressources enfants (collections, documents, etc.).

    this.client.deleteDatabase("/dbs/familydb", null);

## <a id="Run"></a>Étape 11 : exécuter votre application console C#
Pour exécuter l’application à partir de la console, accédez au dossier du projet et compilez à l’aide de Maven :
    
    mvn package

L’exécution de `mvn package` télécharge la dernière bibliothèque Azure Cosmos DB à partir de Maven et génère `GetStarted-0.0.1-SNAPSHOT.jar`. Ensuite, exécutez l’application comme suit :

    mvn exec:java -D exec.mainClass=GetStarted.Program

Félicitations ! Vous avez terminé ce didacticiel NoSQL et vous disposez d’une application console Java opérationnelle !

## <a name="next-steps"></a>étapes suivantes
* Vous recherchez un didacticiel sur les applications web Java ? Consultez [Créer une application web Java avec Azure Cosmos DB](sql-api-java-application.md).
* Découvrez comment [surveiller un compte Azure Cosmos DB](monitor-accounts.md).
* Exécutez des requêtes sur notre exemple de dataset dans le [Query Playground](https://www.documentdb.com/sql/demo).

[keys]: media/sql-api-get-started/nosql-tutorial-keys.png
