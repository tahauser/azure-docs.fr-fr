---
title: "Azure Cosmos DB : créer une application web avec authentification Xamarin et Facebook | Microsoft Docs"
description: "Cet article présente un exemple de code .NET que vous pouvez utiliser pour vous connecter à Azure Cosmos DB et pour l’interroger."
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 11/29/2017
ms.author: mimig
ms.openlocfilehash: dfaa8d05d999f5528daff875d89e38d4fc442992
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2017
---
# <a name="azure-cosmos-db-build-a-web-app-with-net-xamarin-and-facebook-authentication"></a>Azure Cosmos DB : créer une application web avec authentification .NET, Xamarin et Facebook

[!INCLUDE [cosmos-db-sql-api](../../includes/cosmos-db-sql-api.md)] 

Azure Cosmos DB est le service de base de données multi-modèle de Microsoft distribué à l’échelle mondiale. Rapidement, vous avez la possibilité de créer et d’interroger des documents, des paires clé/valeur, et des bases de données orientées graphe, profitant tous de la distribution à l’échelle mondiale et des capacités de mise à l’échelle horizontale au cœur d’Azure Cosmos DB. 

Ce guide de démarrage rapide explique comment créer, à l’aide du portail Azure, un compte Azure Cosmos DB, une base de données de documents, ainsi qu’une collection. Vous allez ensuite créer et déployer une application web incluant une liste de tâches basée sur l’[API .NET SQL](sql-api-sdk-dotnet.md), [Xamarin](https://www.xamarin.com/) et le moteur d’autorisation Azure Cosmos DB. L’application web avec liste de tâches implémente un modèle de données par utilisateur qui permet aux utilisateurs de se connecter via l’authentification Facebook et de gérer leurs propres tâches.

## <a name="prerequisites"></a>Prérequis

Si vous n’avez pas encore installé Visual Studio 2017, vous pouvez télécharger et utiliser la version **gratuite** [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/). Veillez à activer **le développement Azure** lors de l’installation de Visual Studio.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

## <a name="create-a-database-account"></a>Création d'un compte de base de données

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-collection"></a>Ajouter une collection

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

## <a name="clone-the-sample-application"></a>Clonage de l’exemple d’application

À présent, nous allons cloner une application API SQL à partir de github, configurer la chaîne de connexion et l’exécuter. Vous verrez combien il est facile de travailler par programmation avec des données. 

1. Ouvrez une fenêtre de terminal git, comme git bash, et accédez à un répertoire de travail à l’aide de la commande `cd`.  

2. Exécutez la commande suivante pour cloner l’exemple de référentiel : 

    ```bash
    git clone https://github.com/Azure/azure-documentdb-dotnet.git
    ```

3. Ouvrez ensuite le fichier DocumentDBTodo.sln à partir du dossier samples/xamarin/UserItems/xamarin.forms dans Visual Studio. 

## <a name="review-the-code"></a>Vérifier le code

Le code dans le dossier Xamarin contient :

* L’application Xamarin. L’application stocke les tâches de l’utilisateur dans une collection partitionnée nommée UserItems.
* L’API du répartiteur de jetons de ressource. Une simple API web ASP.NET permettant de répartir les jetons de ressource Azure Cosmos DB entre les utilisateurs connectés de l’application. Les jetons de ressource sont des jetons d’accès de courte durée qui permettent à l’application d’accéder aux données de l’utilisateur connecté.

Le flux de données et d’authentification est illustré dans le diagramme ci-dessous.

* La collection UserItems est créée avec la clé de partition « /userid ». Si vous spécifiez une clé de partition pour une collection, Azure Cosmos DB peut se mettre à l’échelle à l’infini au fur et à mesure que le nombre d’utilisateurs et d’éléments augmente.
* L’application Xamarin permet aux utilisateurs de se connecter avec leurs identifiants Facebook.
* L’application Xamarin utilise le jeton d’accès Facebook pour s’authentifier avec l’API ResourceTokenBroker.
* L’API de répartition de jetons de ressource authentifie la demande à l’aide de la fonctionnalité d’authentification d’App Service et demande un jeton de ressource Azure Cosmos DB avec accès en lecture/écriture à tous les documents partageant la clé de partition de l’utilisateur authentifié.
* Le répartiteur de jetons de ressource renvoie le jeton de ressource à l’application cliente.
* L’application accède aux tâches de l’utilisateur à l’aide du jeton de ressource.

![Application Todo avec des exemples de données](./media/create-sql-api-xamarin-dotnet/tokenbroker.png)
    
## <a name="update-your-connection-string"></a>Mise à jour de votre chaîne de connexion

Maintenant, retournez dans le portail Azure afin d’obtenir les informations de votre chaîne de connexion et de les copier dans l’application.

1. Dans le [portail Azure](http://portal.azure.com/), dans votre compte Azure Cosmos DB, dans le volet de navigation de gauche, cliquez sur **Clés**, puis sur **Clés en lecture-écriture**. Vous utiliserez les boutons Copier sur le côté droit de l’écran pour copier l’URI et la clé primaire dans le fichier Web.config à l’étape suivante.

    ![Affichage et copie d’une clé d’accès rapide dans le portail Azure, panneau Clés](./media/create-sql-api-xamarin-dotnet/keys.png)

2. Dans Visual Studio 2017, ouvrez le fichier Web.config dans le dossier azure-documentdb-dotnet/samples/xamarin/UserItems/ResourceTokenBroker/ResourceTokenBroker. 

3. Copiez votre valeur URI à partir du portail (à l’aide du bouton Copier) et définissez-la comme la valeur de la propriété accountUrl dans le fichier Web.config. 

    `<add key="accountUrl" value="{Azure Cosmos DB account URL}"/>`

4. Puis, copiez votre valeur de clé primaire à partir du portail et définissez-la comme la valeur de la clé accountKey dans Web.config. 

    `<add key="accountKey" value="{Azure Cosmos DB secret}"/>`

Vous venez de mettre à jour votre application avec toutes les informations nécessaires pour communiquer avec Azure Cosmos DB. 

## <a name="build-and-deploy-the-web-app"></a>Générer et déployer l’application web

1. Dans le portail Azure, créez un site web App Service pour héberger l’API de répartition des jetons de ressource.
2. Dans le portail Azure, ouvrez le panneau Paramètres de l’application du site web de l’API de répartition des jetons de ressource. Renseignez les paramètres d’application suivants :

    * accountUrl : URL du compte Azure Cosmos DB à partir de l’onglet Clés de votre compte Azure Cosmos DB.
    * accountKey : clé principale du compte Azure Cosmos DB à partir de l’onglet Clés de votre compte Azure Cosmos DB.
    * databaseId et collectionId de la base de données et de la collection créées

3. Publiez la solution ResourceTokenBroker sur le site web que vous avez créé.

4. Ouvrez le projet Xamarin et accédez à TodoItemManager.cs. Renseignez les valeurs accountURL, collectionId, databaseId, ainsi que resourceTokenBrokerURL comme URL https de base pour le site web de répartition des jetons de ressource.

5. Terminez le didacticiel [Comment configurer votre application App Service de manière à utiliser la connexion Facebook](../app-service/app-service-mobile-how-to-configure-facebook-authentication.md) pour configurer l’authentification Facebook et le site web ResourceTokenBroker.

    Exécutez l'application Xamarin.

## <a name="review-slas-in-the-azure-portal"></a>Consulter les contrats SLA dans le portail Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous ne pensez pas utiliser à nouveau cette application, supprimez toutes les ressources créées par ce démarrage rapide dans le portail Azure en procédant comme suit : 

1. À partir du menu de gauche dans le portail Azure, cliquez sur **Groupes de ressources**, puis sur le nom de la ressource que vous venez de créer. 
2. Dans la page de votre groupe de ressources, cliquez sur **Supprimer**, tapez le nom de la ressource à supprimer dans la zone de texte, puis cliquez sur **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez appris à créer un compte Azure Cosmos DB, à créer une collection à l’aide de l’Explorateur de données, et à concevoir et déployer une application Xamarin. Vous pouvez maintenant importer des données supplémentaires à votre compte Cosmos DB. 

> [!div class="nextstepaction"]
> [Importer des données dans Azure Cosmos DB](import-data.md)
