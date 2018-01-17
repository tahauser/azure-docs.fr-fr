---
title: "Azure Cosmos DB : Créer une application .NET Framework ou Core à l’aide de l’API Graph | Microsoft Docs"
description: "Cet article présente un exemple de code .NET Framework/Core que vous pouvez utiliser pour vous connecter au service Azure Cosmos DB et pour l’interroger."
services: cosmos-db
documentationcenter: 
author: luisbosquez
manager: jhubbard
editor: 
ms.assetid: daacbabf-1bb5-497f-92db-079910703046
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 01/08/2018
ms.author: lbosq
ms.openlocfilehash: c7fff37e1b59fd90952826a1410a8dd8c6931e77
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2018
---
# <a name="azure-cosmos-db-build-a-net-framework-or-core-application-using-the-graph-api"></a>Azure Cosmos DB : Créer une application .NET Framework ou Core à l’aide de l’API Graph

Azure Cosmos DB est le service de base de données multi-modèle de Microsoft distribué à l’échelle mondiale. Rapidement, vous avez la possibilité de créer et d’interroger des documents, des paires clé/valeur, et des bases de données orientées graphe, profitant tous de la distribution à l’échelle mondiale et des capacités de mise à l’échelle horizontale au cœur d’Azure Cosmos DB. 

Ce guide de démarrage rapide explique comment créer un compte, une base de données et un graphique (conteneur) Azure Cosmos DB à l’aide du portail Azure. Vous créerez et exécuterez ensuite une application console basée sur l’[API Graph](graph-sdk-dotnet.md).  

## <a name="prerequisites"></a>configuration requise

Si vous n’avez pas encore installé Visual Studio 2017, vous pouvez télécharger et utiliser la version **gratuite** [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/). Veillez à activer **le développement Azure** lors de l’installation de Visual Studio.

Si Visual Studio 2017 est déjà installé, assurez-vous de disposer de [Visual Studio 2017 Update 3](https://www.visualstudio.com/en-us/news/releasenotes/vs2017-relnotes).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Création d'un compte de base de données

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>Ajout d’un graphique

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

## <a name="clone-the-sample-application"></a>Clonage de l’exemple d’application

À présent, nous allons cloner une application API Graph à partir de GitHub, configurer la chaîne de connexion et l’exécuter. Vous verrez combien il est facile de travailler par programmation avec des données. 

Cet exemple de projet utilise le format .NET Core et a été configuré pour cibler les infrastructures suivantes :
 - netcoreapp2.0
 - net461

1. Ouvrez une fenêtre de terminal git, comme git bash, et accédez à un répertoire de travail à l’aide de la commande `cd`.  

2. Exécutez la commande suivante pour cloner l’exemple de référentiel. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-dotnet-getting-started.git
    ```

3. Ouvrez ensuite Visual Studio et le fichier de solution. 

## <a name="review-the-code"></a>Vérifier le code

Passons rapidement en revue ce qu’il se passe dans l’application. Ouvrez le fichier Program.cs ; vous pouvez constater que ces lignes de code créent les ressources Azure Cosmos DB. 

* Le DocumentClient est initialisé. 

    ```csharp
    using (DocumentClient client = new DocumentClient(
        new Uri(endpoint),
        authKey,
        new ConnectionPolicy { ConnectionMode = ConnectionMode.Direct, ConnectionProtocol = Protocol.Tcp }))
    ```

* Une nouvelle base de données est créée.

    ```csharp
    Database database = await client.CreateDatabaseIfNotExistsAsync(new Database { Id = "graphdb" });
    ```

* Un nouveau graphique est créé.

    ```csharp
    DocumentCollection graph = await client.CreateDocumentCollectionIfNotExistsAsync(
        UriFactory.CreateDatabaseUri("graphdb"),
        new DocumentCollection { Id = "graph" },
        new RequestOptions { OfferThroughput = 1000 });
    ```
* Une série d’étapes Gremlin sont exécutées à l’aide de la méthode `CreateGremlinQuery`.

    ```csharp
    // The CreateGremlinQuery method extensions allow you to execute Gremlin queries and iterate
    // results asychronously
    IDocumentQuery<dynamic> query = client.CreateGremlinQuery<dynamic>(graph, "g.V().count()");
    while (query.HasMoreResults)
    {
        foreach (dynamic result in await query.ExecuteNextAsync())
        {
            Console.WriteLine($"\t {JsonConvert.SerializeObject(result)}");
        }
    }

    ```

## <a name="update-your-connection-string"></a>Mise à jour de votre chaîne de connexion

Maintenant, retournez dans le portail Azure afin d’obtenir les informations de votre chaîne de connexion et de les copier dans l’application.

1. Dans le [portail Azure](http://portal.azure.com/), cliquez sur **Clés**. 

    Copiez la première partie de la valeur de l’URI.

    ![Affichage et copie d’une clé d’accès rapide dans le portail Azure, page Clés](./media/create-graph-dotnet/keys.png)

2. Dans Visual Studio 2017, ouvrez le fichier appsettings.json et collez la valeur sur `FILLME` dans le `endpoint`. 

    `"endpoint": "https://FILLME.documents.azure.com:443/",`

    La valeur de point de terminaison doit maintenant ressembler à ceci :

    `"endpoint": "https://testgraphacct.documents.azure.com:443/",`

3. Copiez votre valeur **CLÉ PRIMAIRE** à partir du portail et définissez-la comme la valeur de la clé AuthKey dans le fichier App.config., puis enregistrez vos changements. 

    `"authkey": "FILLME"`

4. Enregistrez le fichier appsettings.json. 

Vous venez de mettre à jour votre application avec toutes les informations nécessaires pour communiquer avec Azure Cosmos DB. 

## <a name="run-the-console-app"></a>Exécution de l’application console

Avant d’exécuter l’application, il est recommandé de mettre à jour le package *Microsoft.Azure.Graphs* vers la dernière version.

1. Dans Visual Studio, cliquez avec le bouton droit sur le projet **GraphGetStarted** dans l’**Explorateur de solutions**, puis cliquez sur **Gérer les packages NuGet**. 

2. Dans l’onglet **Mises à jour** du Gestionnaire de package NuGet, saisissez *Microsoft.Azure.Graphs* et cochez la case **Inclure les préversions**. 

3. Dans les résultats, mettez à jour la bibliothèque **Microsoft.Azure.Graphs** vers la dernière version du package. Cette opération installe le package de bibliothèque d’extension graphique Azure Cosmos DB et toutes les dépendances.

    Si vous obtenez un message concernant la vérification des modifications apportées à la solution, cliquez sur **OK**. Si vous obtenez un message concernant l’acceptation de la licence, cliquez sur **J’accepte**.

4. Appuyez sur Ctrl + F5 pour exécuter l’application.

   La fenêtre de console affiche les sommets et les bords ajoutés au graphique. Lorsque le script se termine, appuyez deux fois sur ENTRÉE pour fermer la fenêtre de console.

## <a name="browse-using-the-data-explorer"></a>Navigation à l’aide de l’Explorateur de données

Vous pouvez maintenant retourner à l’Explorateur de données dans le Portail Azure pour parcourir et interroger vos nouvelles données graphiques.

1. Dans l’Explorateur de données, la nouvelle base de données apparaît dans le volet Graphique. Développez **graphdb**, **graphcollz**, puis cliquez sur **Graphique**.

2. Cliquez sur le bouton **Appliquer un filtre** pour utiliser la requête par défaut et afficher tous les vertex dans le graphique. Les données générées par l’exemple d’application s’affichent dans le volet Graphiques.

    Vous pouvez agrandir et réduire le graphique, développer l’espace d’affichage du graphique, ajouter des vertex et déplacer des vertex sur la surface d’affichage.

    ![Afficher le graphique dans l’Explorateur de données dans le portail Azure](./media/create-graph-dotnet/graph-explorer.png)

## <a name="review-slas-in-the-azure-portal"></a>Vérification des contrats SLA dans le portail Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous ne pensez pas continuer à utiliser cette application, supprimez toutes les ressources créées durant ce guide de démarrage rapide dans le Portail Azure en procédant de la façon suivante : 

1. Dans le menu de gauche du portail Azure, cliquez sur **Groupes de ressources**, puis sur le nom de la ressource que vous avez créée. 
2. Sur la page de votre groupe de ressources, cliquez sur **Supprimer**, tapez le nom de la ressource à supprimer dans la zone de texte, puis cliquez sur **Supprimer**.

## <a name="next-steps"></a>étapes suivantes

Dans ce guide de démarrage rapide, vous avez appris à créer un compte Azure Cosmos D, à créer un graphique à l’aide de l’Explorateur de données et à exécuter une application. Vous pouvez maintenant générer des requêtes plus complexes et implémenter une logique de traversée de graphique puissante, à l’aide de Gremlin. 

> [!div class="nextstepaction"]
> [Interroger à l’aide de Gremlin](tutorial-query-graph.md)

