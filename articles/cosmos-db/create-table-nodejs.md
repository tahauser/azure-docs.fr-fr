---
title: "Démarrage rapide : API Table avec Node.js - Azure Cosmos DB | Microsoft Docs"
description: "Ce démarrage rapide montre comment utiliser l’API Table d’Azure Cosmos DB pour créer une application avec le portail Azure et Node.js."
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
ms.devlang: node
ms.topic: quickstart
ms.date: 11/15/2017
ms.author: arramac
ms.openlocfilehash: 99f3ddb165fa548ca1d65676bb1f945632c72dd3
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2017
---
# <a name="quickstart-build-a-table-api-app-with-nodejs-and-azure-cosmos-db"></a>Démarrage rapide : Créer une application d’API Table avec Node.js et Azure Cosmos DB

Ce démarrage rapide montre comment utiliser Node.js et [l’API Table](table-introduction.md) d’Azure Cosmos DB pour créer une application en clonant un exemple à partir de GitHub. Ce démarrage rapide vous montre également comment créer un compte Azure Cosmos DB et comment utiliser l’Explorateur de données pour créer des tables et des entités dans le portail web Azure.

Azure Cosmos DB est le service de base de données multi-modèle de Microsoft distribué à l’échelle mondiale. Vous avez la possibilité de créer et d’interroger rapidement des bases de données de documents, de paires clé/valeur, de colonnes larges et de graphes, qui bénéficient toutes des capacités de distribution mondiale et de mise à l’échelle horizontale qui sont au cœur d’Azure Cosmos DB. 

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

Par ailleurs :

* [Node.js](https://nodejs.org/en/) version v0.10.29 ou ultérieure
* [Git](http://git-scm.com/)

## <a name="create-a-database-account"></a>Création d'un compte de base de données

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

2. Exécutez la commande suivante pour cloner l’exemple de dépôt : Cette commande crée une copie de l’exemple d’application sur votre ordinateur. 

    ```bash
    git clone https://github.com/Azure-Samples/storage-table-node-getting-started.git
    ```

3. Ouvrez le fichier de solution dans Visual Studio. 

## <a name="update-your-connection-string"></a>Mise à jour de votre chaîne de connexion

Maintenant, retournez dans le portail Azure afin d’obtenir les informations de votre chaîne de connexion et de les copier dans l’application. Cette opération permet à votre application de communiquer avec votre base de données hébergée. 

1. Dans le [portail Azure](http://portal.azure.com/), cliquez sur **Chaîne de connexion**. 

    ![Affichez et copiez les informations de chaîne de connexion qui se trouvent dans le volet Chaîne de connexion.](./media/create-table-nodejs/connection-string.png)

2. Ouvrez le fichier app.config, puis copiez-y les propriétés de chaîne de connexion.

3. Enregistrez le fichier app.config.

Vous venez de mettre à jour votre application avec toutes les informations nécessaires pour communiquer avec Azure Cosmos DB. 

## <a name="run-the-app"></a>Exécution de l'application

1. Dans la fenêtre de terminal git, exécutez la commande `cd` pour accéder au dossier storage-table-java-getting-started.

    ```git
    cd "C:\git-samples\
storage-table-node-getting-started"
    ```

2. Dans la fenêtre de terminal git, exécutez les commandes suivantes pour démarrer l’application Java.

    ```git
    node ./tableSample.js 
    ```

    La fenêtre de console affiche les données qui sont ajoutées à la nouvelle base de données de table dans Azure Cosmos DB.

    Vous pouvez dès à présent revenir à l’Explorateur de données et voir la requête, modifier et travailler avec ces nouvelles données. 

## <a name="review-slas-in-the-azure-portal"></a>Examiner les SLA dans le Portail Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez appris à créer un compte Azure Cosmos DB, à créer une table à l’aide de l’Explorateur de données, et à exécuter une application.  Maintenant, vous pouvez interroger vos données à l’aide de l’API Table.  

> [!div class="nextstepaction"]
> [Importer des données de table dans l’API Table](table-import.md)
