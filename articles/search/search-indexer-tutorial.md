---
title: "Didacticiel pour l’indexation de bases de données SQL Azure dans Recherche Azure | Microsoft Docs"
description: "Analysez une base de données Azure SQL pour extraire les données pouvant faire l’objet d’une recherche et remplir un index Recherche Azure."
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
tags: 
ms.assetid: 
ms.service: search
ms.devlang: na
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 11/10/2017
ms.author: heidist
ms.openlocfilehash: 22eeccfd692cca74bc0cf12c3400d717c103332f
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="how-to-crawl-an-azure-sql-database-using-azure-search-indexers"></a>Comment analyser une base de données SQL Azure à l’aide d’indexeurs Recherche Azure

Ce didacticiel vous montre comment configurer un indexeur pour extraire des données pouvant faire l’objet d’une recherche à partir d’un exemple de base de données SQL Azure. [Les indexeurs](search-indexer-overview.md) sont un composant de Recherche Azure qui analyse les sources de données externes tout en alimentant un [index de recherche](search-what-is-an-index.md) avec le contenu. De tous les indexeurs, l’indexeur correspondant à la base de données SQL Azure est le plus couramment utilisé. 

Il est utile de maîtriser la configuration de l’indexeur dans la mesure où cela simplifie la quantité de code à écrire et à gérer. Au lieu de préparer et d’envoyer un jeu de données conformes au schéma JSON, vous pouvez attacher un indexeur à une source de données, faire en sorte que l’indexeur extrait des données et les insère dans un index, et vous pouvez également exécuter l’indexeur selon une planification périodique pour récupérer les modifications dans la source sous-jacente.

Dans ce didacticiel, vous effectuez les tâches suivantes à l’aide des [ bibliothèques clientes .NET Recherche Azure](https://aka.ms/search-sdk) et d’une application de console .NET Core :

> [!div class="checklist"]
> * Télécharger et configurer la solution
> * Ajouter des informations de service de recherche aux paramètres d’application
> * Préparer un jeu de données externe dans la base de données SQL Azure 
> * Passer en revue les définitions d’index et d’indexeur dans l’exemple de code
> * Exécuter le code de l’indexeur pour importer des données
> * Rechercher l’index
> * Afficher la configuration de l’indexeur dans le portail

## <a name="prerequisites"></a>Prérequis

* Un compte Azure actif. Si vous n’en avez pas, vous pouvez vous inscrire pour bénéficier d’un [essai gratuit](https://azure.microsoft.com/free/). 

* Un service Azure Search. Pour obtenir une aide à la configuration, consultez [Créer un service de recherche](search-create-service-portal.md).

* Une base de données SQL Azure fournissant la source de données externe utilisée par un indexeur. L’exemple de solution fournit un fichier de données SQL pour créer la table.

* Visual Studio 2017. Vous pouvez utiliser gratuitement [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/). 

> [!Note]
> Si vous utilisez le service Recherche Azure gratuit, vous êtes limité à trois index, trois indexeurs et trois sources de données. Ce didacticiel crée une occurrence de chaque élément. Assurez-vous de disposer de l’espace suffisant sur votre service pour accepter les nouvelles ressources.

## <a name="download-the-solution"></a>Télécharger la solution

La solution d’indexeur utilisée dans ce didacticiel est issue d’une collection d’exemples Recherche Azure fournie dans un téléchargement maître unique. La solution utilisée pour ce didacticiel est *DotNetHowToIndexers*.

1. Accédez à [**Azure-Samples/search-dotnet-getting-started**](https://github.com/Azure-Samples/search-dotnet-getting-started) dans le référentiel GitHub d’exemples Azure.

2. Cliquez sur **Cloner ou télécharger** > **Télécharger le ZIP**. Par défaut, le fichier est placé dans le dossier Téléchargements.

3. Dans l’**Explorateur de fichiers** > **Téléchargements**, cliquez avec le bouton droit sur le fichier et choisissez **Extraire tout**.

4. Désactivez les autorisations en lecture seule. Cliquez avec le bouton droit sur le nom de dossier > **Propriétés** > **Général**, puis désactivez l’attribut **Lecture seule** pour le dossier actif, les sous-dossiers et les fichiers.

5. Dans **Visual Studio 2017**, ouvrez la solution *DotNetHowToIndexers.sln*.

6. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur la solution parente du nœud supérieur > **Restaurer les packages Nuget**.

## <a name="set-up-connections"></a>Configurer les connexions
Les informations de connexion aux services requis sont spécifiées dans le fichier **appsettings.json** de la solution. 

Dans l’Explorateur de solutions, ouvrez **appsettings.json** afin de pouvoir alimenter chaque paramètre en suivant les instructions de ce didacticiel.  

```json
{
  "SearchServiceName": "Put your search service name here",
  "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
  "AzureSqlConnectionString": "Put your Azure SQL database connection string here",
}
```

### <a name="get-the-search-service-name-and-admin-api-key"></a>Obtenir le nom du service de recherche et la clé d’API d’administrateur

Vous trouverez la clé et le point de terminaison du service de recherche dans le portail. Une clé donne accès aux opérations de service. Les clés d’administration permettent l’accès en écriture requis pour créer et supprimer des objets, tels que des index et des indexeurs, dans votre service.

1. Connectez-vous au [portail Azure](https://portal.azure.com/) et passez en revue les [services de recherche de votre abonnement](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices).

2. Ouvrez la page de service.

3. En haut, recherchez le nom du service dans la page principale. Dans la capture d’écran suivante, il s’agit de *azs-tutorial*.

   ![Nom du service](./media/search-indexer-tutorial/service-name.png)

4. Copiez et collez-le en tant que première entrée dans **appsettings.json** dans Visual Studio.

  > [!Note]
  > Un nom de service fait partie du point de terminaison qui inclut search.windows.net. Si cela vous intéresse, vous pouvez afficher l’URL complète dans **Essentials** sur la page de présentation. L’URL ressemble à cet exemple : https://your-service-name.search.windows.net

5. Sur la gauche, dans **Paramètres** > **Clés**, copiez une des clés d’administration et collez-la comme deuxième entrée dans i**appsettings.json**. Les clés sont des chaînes alphanumériques générées pour votre service pendant l’approvisionnement et requises pour disposer d’un accès autorisé aux opérations de service. 

  Après avoir ajouté les deux paramètres, votre fichier doit ressembler à l’exemple suivant :

  ```json
  {
    "SearchServiceName": "azs-tutorial",
    "SearchServiceAdminApiKey": "A1B2C3D4E5F6G7H8I9J10K11L12M13N14",
    . . .
  }
  ```

## <a name="prepare-an-external-data-source"></a>Préparer une source de données externe

Au cours de cette étape, créez une source de données externe qu’un indexeur peut analyser. Le fichier de données de ce didacticiel est *hotels.sql*, fourni dans le dossier de solutions \DotNetHowToIndexers. 

### <a name="azure-sql-database"></a>Base de données SQL Azure

Vous pouvez utiliser le portail Azure et le fichier *hotels.sql* à partir de l’exemple pour créer le jeu de données dans la base de données SQL Azure. Recherche Azure utilise des ensembles de lignes aplatis, tel que celui généré à partir d’une vue ou d’une requête. Le fichier SQL de l’exemple de solution crée et remplit une table unique.

L’exercice suivant suppose l’absence de serveur ou de base de données existante et vous invite à créer les deux lors de l’étape 2. Éventuellement, si vous disposez d’une ressource, vous pouvez y ajouter la table hôtels, en commençant à l’étape 4.

1. Connectez-vous au [Portail Azure](https://portal.azure.com/). 

2. Cliquez sur **Créer une ressource** > **Base de données SQL** pour créer une base de données, un serveur et un groupe de ressources. Vous pouvez utiliser les valeurs par défaut et le niveau de tarification le moins élevé. Un des avantages lié à la création d’un serveur est de pouvoir spécifier un nom d’utilisateur administrateur et le mot de passe nécessaire pour la création et le chargement des tables lors d’une étape ultérieure.

   ![Nouvelle page de base de données](./media/search-indexer-tutorial/indexer-new-sqldb.png)

3. Cliquez sur **Créer** pour déployer le nouveau serveur et la base de données. Patientez jusqu’au déploiement du serveur et de la base de données.

4. Ouvrez la page Base de données SQL de votre nouvelle base de données, si elle n’est pas encore ouverte. Le nom de la ressource doit indiquer *Base de données SQL* et non *SQL Server*.

  ![Page de la base de données SQL](./media/search-indexer-tutorial/hotels-db.png)

4. Dans la barre de commandes, cliquez sur **Outils** > **Éditeur de requête**.

5. Cliquez sur **Connexion** et entrez le nom d’utilisateur et un mot de passe d’administrateur du serveur.

6. Cliquez sur **Ouvrir la requête** et accédez à l’emplacement d’*hotels.sql*. 

7. Sélectionnez le fichier et cliquez sur **Ouvrir**. Le script doit ressembler à la capture d’écran suivante :

  ![Script SQL](./media/search-indexer-tutorial/sql-script.png)

8. Cliquez sur **Exécuter** pour exécuter la requête. Dans le volet Résultats, vous devez voir un message de réussite de la requête pour 3 lignes.

9. Pour renvoyer un ensemble de lignes de cette table, vous pouvez exécuter la requête suivante comme étape de vérification :

   ```sql
   SELECT HotelId, HotelName, Tags FROM Hotels
   ```
   Le prototype de requête `SELECT * FROM Hotels` ne fonctionne pas dans l’éditeur de requête. L’exemple de données inclut les coordonnées géographiques du champ Emplacement qui n’est pas géré dans l’éditeur pour l’instant. Pour obtenir la liste des autres colonnes de la requête, vous pouvez exécuter l’instruction suivante : `SELECT * FROM sys.columns WHERE object_id = OBJECT_ID('dbo.Hotels')`

10. Maintenant que vous avez un jeu de données externe, copiez la chaîne de connexion ADO.NET pour la base de données. Sur la page Base de données SQL de votre base de données, accédez à **Paramètres** > **Chaînes de connexion**, puis copiez la chaîne de connexion ADO.NET.
 
  Une chaîne de connexion ADO.NET ressemble à l’exemple suivant, modifié pour utiliser un nom de base de données, un nom d’utilisateur et un mot de passe valides.

  ```sql
  Server=tcp:hotels-db.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
  ```
11. Collez la chaîne de connexion « AzureSqlConnectionString » comme troisième entrée du fichier **appsettings.json** dans Visual Studio.

    ```json
    {
      "SearchServiceName": "azs-tutorial",
      "SearchServiceAdminApiKey": "A1B2C3D4E5F6G7H8I9J10K11L12M13N14",
      "AzureSqlConnectionString": "Server=tcp:hotels-db.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security  Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;",
    }
    ```

## <a name="understand-index-and-indexer-code"></a>Comprendre le code de l’index et de l’indexeur

Votre code est maintenant prêt à créer et à exécuter. Avant cela, prenez une minute pour étudier les définitions d’index et d’indexeur de cet exemple. Le code qui convient se trouve dans deux fichiers :

  + **Hotel.cs**, contenant le schéma qui définit l’index
  + **Program.cs**, contenant les fonctions permettant de créer et de gérer des structures dans votre service

### <a name="in-hotelcs"></a>Dans Hotel.cs

Le schéma d’index définit la collection de champs, y compris les attributs spécifiant les opérations autorisées, pour savoir, par exemple, si un champ peut faire l’objet d’une interrogation en texte intégral, d’un filtrage ou d’un tri comme indiqué dans la définition de champ suivante pour HotelName. 

```csharp
. . . 
[IsSearchable, IsFilterable, IsSortable]
public string HotelName { get; set; }
. . .
```

Un schéma peut également inclure d’autres éléments, y compris des profils de notation pour améliorer un score de recherche, des analyseurs personnalisés et d’autres constructions. Toutefois, en ce qui nous concerne, le schéma est peu défini et comporte uniquement des champs trouvés dans les exemples de jeux de données.

Dans ce didacticiel, l’indexeur extrait les données d’une source de données. Dans la pratique, vous pouvez attacher plusieurs indexeurs au même index en créant un index consolidé pouvant faire l’objet de recherches à partir de plusieurs sources de données et indexeurs. Vous pouvez utiliser la même paire index/indexeur en variant juste les sources de données ou un index avec diverses combinaisons d’indexeurs et de sources de données, en fonction de vos besoins en matière de flexibilité.

### <a name="in-programcs"></a>Dans Program.cs

Le programme principal inclut des fonctions pour l’ensemble des trois sources de données représentatives. Si l’on se concentre juste sur Microsoft Azure SQL Database, les objets suivants ressortent :

  ```csharp
  private const string IndexName = "hotels";
  private const string AzureSqlHighWaterMarkColumnName = "RowVersion";
  private const string AzureSqlDataSourceName = "azure-sql";
  private const string AzureSqlIndexerName = "azure-sql-indexer";
  ```

Dans Recherche Azure, les objets que vous pouvez afficher, configurer ou supprimer indépendamment comprennent les index, les indexeurs et les sources de données (respectivement *hotels*, *azure-sql-indexer*, *azure-sql*). 

La colonne *AzureSqlHighWaterMarkColumnName* mérite une attention particulière, car elle fournit des informations de détection de modification utilisées par l’indexeur pour déterminer si une ligne a changé depuis la dernière charge de travail d’indexation. Les [stratégies de détection des modifications](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) sont prises en charge uniquement dans les indexeurs et varient selon la source de données. Pour Microsoft Azure SQL Database deux stratégies sont à votre disposition, selon les exigences liées à la base de données.

Le code suivant montre les méthodes du fichier Program.cs utilisé pour la création d’une source de données et d’un indexeur. Le code recherche et supprime les ressources existantes du même nom, en supposant que vous pouvez exécuter ce programme plusieurs fois.

  ```csharp
  private static string SetupAzureSqlIndexer(SearchServiceClient serviceClient, IConfigurationRoot configuration)
  {
    Console.WriteLine("Deleting Azure SQL data source if it exists...");
    DeleteDataSourceIfExists(serviceClient, AzureSqlDataSourceName);

    Console.WriteLine("Creating Azure SQL data source...");
    DataSource azureSqlDataSource = CreateAzureSqlDataSource(serviceClient, configuration);

    Console.WriteLine("Deleting Azure SQL indexer if it exists...");
    DeleteIndexerIfExists(serviceClient, AzureSqlIndexerName);

    Console.WriteLine("Creating Azure SQL indexer...");
    Indexer azureSqlIndexer = CreateIndexer(serviceClient, AzureSqlDataSourceName, AzureSqlIndexerName);

    return azureSqlIndexer.Name;
  }
  ```

Notez que les appels d’API de l’indexeur sont sans plateforme à l’exception de [DataSourceType](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype?view=azure-dotnet), qui spécifie le type d’analyseur à appeler.

## <a name="run-the-indexer"></a>Exécuter l’indexeur

Dans cette étape, compilez et exécutez le programme. 

1. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur **DotNetHowToIndexers** et sélectionnez **Build**.
2. Là encore, avec le bouton droit cliquez sur **DotNetHowToIndexers**, puis sur **Déboguer** > **Démarrer une nouvelle instance**.

Le programme s’exécute en mode débogage. Une fenêtre de console signale l’état de chaque opération.

  ![Script SQL](./media/search-indexer-tutorial/console-output.png)

Votre code s’exécute localement dans Visual Studio en se connectant à votre service de recherche sur Azure, qui à son tour utilise la chaîne de connexion pour se connecter à Microsoft Azure SQL Database et récupérer le jeu de données. Ces nombreuses opérations sont source de plusieurs points de défaillance potentiels. Si vous obtenez une erreur, vérifiez tout d’abord les conditions suivantes :

+ Les informations de connexion du service de recherche que vous fournissez se limitent au nom du service de ce didacticiel. Si vous avez entré l’URL complète, les opérations s’arrêtent à la création d’index avec une erreur signalant un échec de connexion.

+ Informations de connexion à la base de données dans **appsettings.json**. Il doit s’agir de la chaîne de connexion ADO.NET obtenue à partir du portail et modifiée pour inclure un nom d’utilisateur et un mot de passe valides pour votre base de données. Le compte d’utilisateur doit avoir l’autorisation de récupérer des données.

+ Limites des ressources. Rappelez-vous que le service (gratuit) partagé se limite à 3 index, indexeurs et sources de données. Un service ayant atteint la limite maximale ne peut pas créer de nouveaux objets.

## <a name="search-the-index"></a>Rechercher l’index 

Dans le portail Azure, dans la page de vue d’ensemble du service de recherche, cliquez sur **Explorateur de recherche** en haut pour soumettre des requêtes sur le nouvel index.

1. Cliquez sur **Modifier l’index** en haut pour sélectionner l’index *hotels*.

2. Cliquez sur le bouton **Rechercher** pour lancer une recherche vide. 

  Les trois entrées de votre index sont renvoyées en tant que documents JSON. L’explorateur de recherche renvoie des documents au format JSON afin que vous puissiez afficher l’ensemble de la structure.

3. Ensuite, entrez une chaîne de recherche : `search=river&$count=true`. 

  Cette requête appelle la recherche en texte intégral sur le terme `river` et le résultat inclut le nombre de documents correspondants. Connaître le nombre de documents correspondants est utile dans les scénarios de test lorsque vous avez un index de grande taille comportant des milliers, voire des millions de documents. Dans ce cas, un seul document correspond à la requête.

4. Enfin, entrez une chaîne de recherche qui limite la sortie JSON aux champs pertinents : `search=river&$count=true&$select=hotelId, baseRate, description`. 

  La réponse à la requête se réduit aux champs sélectionnés, ce qui entraîne une sortie plus concise.

## <a name="view-indexer-configuration"></a>Afficher la configuration de l’indexeur

Tous les indexeurs, y compris celui que vous venez de créer par programme, sont répertoriés dans le portail. Vous pouvez ouvrir une définition d’indexeur et afficher sa source de données ou configurer une planification d’actualisation pour sélectionner les lignes nouvelles et modifiées.

1. Ouvrez la page de vue d’ensemble de votre service Recherche Azure.
2. Faites défiler vers le bas pour rechercher les vignettes pour **Indexeurs** et **Sources de données**.
3. Cliquez sur une vignette pour ouvrir une liste de chaque ressource. Vous pouvez sélectionner des indexeurs ou des sources de données pour afficher ou modifier les paramètres de configuration.

  ![Vignettes d’indexeur et de source de données](./media/search-indexer-tutorial/tiles-portal.png)

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous ne prévoyez pas de continuer à utiliser ces services, utilisez les étapes suivantes pour supprimer toutes les ressources créées par ce didacticiel dans le portail Azure. 

1. Dans le menu de gauche du portail Azure, cliquez sur **Groupes de ressources**, puis sur le nom de la ressource que vous avez créée. 
2. Dans la page de votre groupe de ressources, cliquez sur **Supprimer le groupe de ressources**, tapez le nom de la ressource à supprimer dans la zone de texte, puis cliquez sur **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations et pour connaître les tâches spécifiques aux autres sources de données pris en charge, consultez les articles suivants :

* [Base de données SQL Azure ou SQL Server sur une machine virtuelle Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Stockage Table Azure](search-howto-indexing-azure-tables.md)
* [Stockage Blob Azure](search-howto-indexing-azure-blob-storage.md)
* [Indexation d’objets blob CSV avec l’indexeur d’objets blob Recherche Azure](search-howto-index-csv-blobs.md)
* [Indexation d’objets blob JSON avec l’indexeur d’objets blob Recherche Azure](search-howto-index-json-blobs.md)

