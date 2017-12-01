---
title: "Démarrage rapide : API Table avec Java - Azure Cosmos DB | Microsoft Docs"
description: "Ce guide de démarrage rapide montre comment utiliser l’API Table Azure Cosmos DB pour créer une application avec le portail Azure et Java"
services: cosmos-db
documentationcenter: 
author: arramac
manager: jhubbard
editor: 
ms.assetid: 66327041-4d5e-4ce6-a394-fee107c18e59
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: quickstart
ms.date: 11/20/2017
ms.author: arramac
ms.openlocfilehash: 90ba10990049cd1fb788d63a143eb1169191cf24
ms.sourcegitcommit: 1d8612a3c08dc633664ed4fb7c65807608a9ee20
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/20/2017
---
# <a name="quickstart-build-a-table-api-app-with-java-and-azure-cosmos-db"></a>Démarrage rapide : Créer une application d’API Table avec Java et Azure Cosmos DB

Ce guide de démarrage rapide montre comment utiliser Java et [l’API Table](table-introduction.md) Azure Cosmos DB pour créer une application en clonant un exemple de GitHub. Ce guide de démarrage rapide vous montre également comment créer un compte Azure Cosmos DB et comment utiliser l’Explorateur de données pour créer des tables et des entités dans le portail web Azure.

Azure Cosmos DB est le service de base de données multi-modèle de Microsoft distribué à l’échelle mondiale. Rapidement, vous avez la possibilité de créer et d’interroger des documents, des paires clé/valeur, et des bases de données orientées graphe, profitant tous de la distribution à l’échelle mondiale et des capacités de mise à l’échelle horizontale au cœur d’Azure Cosmos DB. 

## <a name="prerequisites"></a>Composants requis

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

Par ailleurs : 

* [Java Development Kit (JDK) 1.7+](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
    * Sur Ubuntu, exécutez `apt-get install default-jdk` pour installer le JDK.
    * Veillez à définir la variable d’environnement JAVA_HOME pour qu’elle pointe vers le dossier dans lequel le JDK est installé.
* [Téléchargement](http://maven.apache.org/download.cgi) et [installation](http://maven.apache.org/install.html) d’une archive binaire [Maven](http://maven.apache.org/)
    * Sur Ubuntu, vous pouvez exécuter `apt-get install maven` pour installer Maven.
* [Git](https://www.git-scm.com/)
    * Sur Ubuntu, vous pouvez exécuter `sudo apt-get install git` pour installer Git.

## <a name="create-a-database-account"></a>Création d'un compte de base de données

> [!IMPORTANT] 
> Vous devez créer un compte d’API Table pour utiliser les kits SDK d’API Table mis à la disposition générale. Les comptes d’API Table créés pendant la durée de la préversion ne sont pas pris en charge par les kits SDK mis à la disposition générale.
>

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

## <a name="add-a-table"></a>Ajouter une table

[!INCLUDE [cosmos-db-create-table](../../includes/cosmos-db-create-table.md)]

## <a name="add-sample-data"></a>Ajouter un exemple de données

Vous pouvez maintenant ajouter des données à votre nouvelle table grâce à l’Explorateur de données.

1. Dans l’Explorateur de données, développez **exemple de table**, cliquez sur **Entités**, puis cliquez sur **Ajouter une entité**.

   ![Créer des entités dans l’Explorateur de données, dans le portail Azure](./media/create-table-dotnet/azure-cosmosdb-data-explorer-new-document.png)
2. À présent, ajoutez des données à la zone de valeur PartitionKey et aux zones de valeur RowKey, puis cliquez sur **Ajouter une entité**.

   ![Définir la clé de Partition et la clé de ligne pour une nouvelle entité](./media/create-table-dotnet/azure-cosmosdb-data-explorer-new-entity.png)
  
    Vous pouvez maintenant ajouter des entités supplémentaires à votre table, éditer vos entités, ou interroger vos données dans l’Explorateur de données. À partir de l’Explorateur de données, vous pouvez également faire évoluer votre débit et ajouter des procédures stockées, des fonctions définies par l’utilisateur, et ajouter à votre table des déclencheurs.

## <a name="clone-the-sample-application"></a>Clonage de l’exemple d’application

À présent, nous allons cloner une application Table à partir de GitHub, configurer la chaîne de connexion et l’exécuter. Vous verrez combien il est facile de travailler par programmation avec des données. 

1. Ouvrez une fenêtre de terminal git comme Git Bash et utilisez la commande `cd` pour accéder à un dossier d’installation pour l’exemple d’application. 

    ```bash
    cd "C:\git-samples"
    ```

2. Exécutez la commande suivante pour cloner l’exemple de référentiel : Cette commande crée une copie de l’exemple d’application sur votre ordinateur. 

    ```bash
    git clone https://github.com/Azure-Samples/storage-table-java-getting-started.git 
    ```

## <a name="update-your-connection-string"></a>Mise à jour de votre chaîne de connexion

Maintenant, retournez dans le portail Azure afin d’obtenir les informations de votre chaîne de connexion et de les copier dans l’application. Cette opération permet à votre application de communiquer avec votre base de données hébergée. 

1. Dans le [portail Azure](http://portal.azure.com/), cliquez sur **Chaîne de connexion**. 

   ![Affichez et copiez les informations de chaîne de connexion qui se trouvent dans le volet Chaîne de connexion](./media/create-table-java/connection-string.png)

2. Copiez la CHAÎNE DE CONNEXION PRINCIPALE à l’aide du bouton Copier à droite.

3. Ouvrez le fichier config.properties dans le dossier C:\git-samples\storage-table-java-getting-started\src\main\resources. 

5. Commentez la ligne une et supprimez les commentaires de la ligne deux. Les deux premières lignes devraient maintenant ressembler à ceci.

    ```
    #StorageConnectionString = UseDevelopmentStorage=true
    StorageConnectionString = DefaultEndpointsProtocol=https;AccountName=[ACCOUNTNAME];AccountKey=[ACCOUNTKEY]
    ```

6. Collez votre CHAÎNE DE CONNEXION PRINCIPALE du portail dans la valeur StorageConnectionString à la ligne 2. 

    > [!IMPORTANT]
    > Si votre point de terminaison utilise documents.azure.com, cela signifie que vous disposez d’un compte de version préliminaire et que vous devez créer un [nouveau compte d’API Table](#create-a-database-account) à utiliser avec le Kit de développement logiciel (SDK) d’API Table généralement disponible.
    >

7. Enregistrez le fichier config.properties.

Vous venez de mettre à jour votre application avec toutes les informations nécessaires pour communiquer avec Azure Cosmos DB. 

## <a name="run-the-app"></a>Exécution de l'application

1. Dans la fenêtre de terminal git, exécutez `cd` pour accéder au dossier storage-table-java-getting-started.

    ```git
    cd "C:\git-samples\storage-table-java-getting-started"
    ```

2. Dans la fenêtre de terminal git, exécutez les commandes suivantes pour démarrer l’application Java.

    ```git
    mvn compile exec:java 
    ```

    La fenêtre de console affiche les données de table qui sont ajoutées à la nouvelle base de données de tables dans Azure Cosmos DB.

    Vous pouvez dès à présent revenir à l’Explorateur de données et voir la requête, modifier et travailler avec ces nouvelles données. 

## <a name="review-slas-in-the-azure-portal"></a>Examiner les SLA dans le Portail Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez appris à créer un compte Azure Cosmos DB, à créer une table à l’aide de l’Explorateur de données, et à exécuter une application.  Maintenant, vous pouvez interroger vos données à l’aide de l’API Table.  

> [!div class="nextstepaction"]
> [Importer des données de table dans l’API Table](table-import.md)
