---
title: "Azure Cosmos DB : Didacticiel pour bien démarrer avec l’API SQL et .NET Core | Microsoft Docs"
description: "Didacticiel qui crée une application de base de données en ligne et de console C# à l’aide du SDK .NET Core de l’API SQL d’Azure Cosmos DB."
services: cosmos-db
documentationcenter: .net
author: arramac
manager: jhubbard
editor: 
ms.assetid: 9f93e276-9936-4efb-a534-a9889fa7c7d2
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/15/2017
ms.author: arramac
ms.custom: devcenter
ms.openlocfilehash: 0b19071bf871029b488b26d3f125d08d7d2a2dd4
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/18/2017
---
# <a name="azure-cosmos-db-getting-started-with-the-sql-api-and-net-core"></a>Azure Cosmos DB : Bien démarrer avec l’API SQL et .NET Core
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

Bienvenue dans le didacticiel Bien démarrer avec l’API SQL pour Azure Cosmos DB avec .NET Core À la fin de ce didacticiel, vous disposerez d’une application de console qui crée et interroge des ressources Azure Cosmos DB.

Ce didacticiel contient les sections suivantes :

* Création et connexion à un compte Azure Cosmos DB
* Configuration de votre solution Visual Studio
* Création d’une base de données en ligne
* Création d’une collection
* Création de documents JSON
* Interrogation de la collection
* Remplacement d'un document
* Suppression d’un document
* Suppression de la base de données

Vous n’avez pas le temps ? Ne vous inquiétez pas ! La solution complète est disponible sur [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-core-getting-started). Pour obtenir des instructions rapides, allez à la section [Obtenir la solution complète](#GetSolution) .

Vous voulez créer une application Xamarin iOS, Android ou Forms à l’aide de l’API SQL et du SDK .NET Core ? Consultez la page [Créer des applications mobiles avec Xamarin et Azure Cosmos DB](mobile-apps-with-xamarin.md).

> [!NOTE]
> Le SDK .NET Core Azure Cosmos DB utilisé dans ce didacticiel n’est pas encore compatible avec les applications UWP (Universal Windows Platform). Pour obtenir une version préliminaire du Kit de développement logiciel (SDK) .NET Core qui prenne en charge les applications UWP, envoyez un e-mail à l’adresse [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com).

Commençons dès maintenant !

## <a name="prerequisites"></a>configuration requise

* Un compte Azure actif. Si vous n’en avez pas, vous pouvez vous inscrire pour bénéficier d’un [compte gratuit](https://azure.microsoft.com/free/). 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)] 
    * Si vous travaillez sur un système Mac OS ou Linux, vous pouvez développer des applications .NET Core à partir de la ligne de commande en installant le [SDK .NET Core](https://www.microsoft.com/net/core#macos) pour la plateforme de votre choix. 
    * Si vous travaillez sur un système Windows, vous pouvez développer des applications .NET Core à partir de la ligne de commande en installant le [SDK .NET Core](https://www.microsoft.com/net/core#windows). 
    * Vous pouvez utiliser votre propre éditeur ou télécharger [Visual Studio Code](https://code.visualstudio.com/), qui est disponible gratuitement pour les systèmes Windows, Linux et Mac OS. 

## <a name="step-1-create-an-azure-cosmos-db-account"></a>Étape 1 : créer un compte Azure Cosmos DB
Commençons par créer un compte Azure Cosmos DB. Si vous avez déjà un compte que vous souhaitez utiliser, vous pouvez passer directement à l’étape [Configurer votre solution Visual Studio](#SetupVS). Si vous utilisez l’émulateur Azure Cosmos DB, suivez les étapes de la section [Émulateur Azure Cosmos DB](local-emulator.md) pour le configurer, puis passez directement à l’étape [Configurer votre solution Visual Studio](#SetupVS).

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="SetupVS"></a>Étape 2 : configurer votre solution Visual Studio
1. Ouvrez **Visual Studio 2017** sur votre ordinateur.
2. Dans le menu **Fichier**, sélectionnez **Nouveau**, puis choisissez **Projet**.
3. Dans la boîte de dialogue **Nouveau projet**, sélectionnez **Modèles** / **Visual C#** / **.NET Core**/**Application console (.NET Core)**, nommez votre projet **DocumentDBGettingStarted**, puis cliquez sur **OK**.

   ![Capture d’écran de la fenêtre Nouveau projet](./media/sql-api-dotnetcore-get-started/nosql-tutorial-new-project-2.png)
4. Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur **DocumentDBGettingStarted**.
5. Ensuite, sans quitter le menu, cliquez sur **Gérer les packages NuGet...**

   ![Capture d'écran du menu du clic droit pour le projet](./media/sql-api-dotnetcore-get-started/nosql-tutorial-manage-nuget-pacakges.png)
6. Dans l’onglet **NuGet**, cliquez sur **Parcourir** en haut de la fenêtre, puis tapez **azure documentdb** dans la zone de recherche.
7. Dans les résultats, recherchez **Microsoft.Azure.DocumentDB.Core** et cliquez sur **Installer**.
   L’ID de package de la bibliothèque est [Microsoft.Azure.DocumentDB.Core](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core). Si vous ciblez une version de .NET Framework (net461, par exemple) qui n’est pas prise en charge par ce package NuGet .NET Core, utilisez [Microsoft.Azure.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB) qui prend en charge toutes les versions de .NET Framework à partir de la version 4.5.
8. Aux invites, acceptez les installations de packages NuGet et le contrat de licence.

Parfait ! Maintenant que la configuration est terminée, nous pouvons commencer l’écriture du code. Vous trouverez le projet de code complet de ce didacticiel dans [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-core-getting-started).

## <a id="Connect"></a>Étape 3 : se connecter à un compte Azure Cosmos DB
Tout d’abord, ajoutez ces références au début de votre application C#, dans le fichier Program.cs :

```csharp
using System;

// ADD THIS PART TO YOUR CODE
using System.Linq;
using System.Threading.Tasks;
using System.Net;
using Microsoft.Azure.Documents;
using Microsoft.Azure.Documents.Client;
using Newtonsoft.Json;
```

> [!IMPORTANT]
> Pour pouvoir exécuter ce didacticiel, veillez à ajouter les dépendances ci-dessus.

Maintenant, ajoutez ces deux constantes et votre variable *client* dans votre classe publique *Program*.

```csharp
class Program
{
    // ADD THIS PART TO YOUR CODE
    private const string EndpointUri = "<your endpoint URI>";
    private const string PrimaryKey = "<your key>";
    private DocumentClient client;
```

Ensuite, accédez au [portail Azure](https://portal.azure.com) pour récupérer votre URI et votre clé primaire. L’URI et la clé primaire Azure Cosmos DB sont nécessaires pour que votre application sache où se connecter et qu’Azure Cosmos DB approuve la connexion de votre application.

Dans le portail Azure, accédez à votre compte Azure Cosmos DB, puis cliquez sur **Clés**.

Copiez l’URI à partir du portail et collez-le dans `<your endpoint URI>` dans le fichier program.cs. Ensuite, copiez la clé primaire à partir du portail, puis collez-la dans `<your key>`. Si vous utilisez l’émulateur Azure Cosmos DB, utilisez `https://localhost:8081` comme point de terminaison et la clé d’autorisation bien définie dans [How to develop using the Azure Cosmos DB Emulator](local-emulator.md) (Comment développer à l’aide de l’émulateur Azure Cosmos DB). Assurez-vous de supprimer le < et > , mais laissez les guillemets doubles autour de votre point de terminaison et de la clé.

![Capture d’écran du portail Azure utilisée par le didacticiel NoSQL pour créer une application de console C#. Présente un compte Azure Cosmos DB, avec le hub ACTIF et le bouton CLÉS mis en surbrillance sur la page du compte Azure Cosmos DB, et les valeurs d’URI, de CLÉ PRIMAIRE et de CLÉ SECONDAIRE mises en surbrillance sur la page Clés][keys]

Nous allons démarrer l’application de prise en main en créant une instance de **DocumentClient**.

Sous la méthode **Main**, ajoutez la nouvelle tâche asynchrone appelée **GetStartedDemo** qui va instancier notre nouveau **DocumentClient**.

```csharp
static void Main(string[] args)
{
}

// ADD THIS PART TO YOUR CODE
private async Task GetStartedDemo()
{
    this.client = new DocumentClient(new Uri(EndpointUri), PrimaryKey);
}
```

Ajoutez le code suivant pour exécuter votre tâche asynchrone à partir de la méthode **Main** . La méthode **Main** intercepte les exceptions et les consigne dans la console.

```csharp
static void Main(string[] args)
{
        // ADD THIS PART TO YOUR CODE
        try
        {
                Program p = new Program();
                p.GetStartedDemo().Wait();
        }
        catch (DocumentClientException de)
        {
                Exception baseException = de.GetBaseException();
                Console.WriteLine("{0} error occurred: {1}, Message: {2}", de.StatusCode, de.Message, baseException.Message);
        }
        catch (Exception e)
        {
                Exception baseException = e.GetBaseException();
                Console.WriteLine("Error: {0}, Message: {1}", e.Message, baseException.Message);
        }
        finally
        {
                Console.WriteLine("End of demo, press any key to exit.");
                Console.ReadKey();
        }
```

Appuyez sur le bouton **DocumentDBGettingStarted** pour générer et exécuter l’application.

Félicitations ! Vous êtes connecté à un compte Azure Cosmos DB. Jetons maintenant un œil à l’utilisation des ressources Azure Cosmos DB.  

## <a name="step-4-create-a-database"></a>Étape 4 : créer une base de données
Avant d'ajouter le code permettant de créer une base de données, ajoutez une méthode d'assistance pour l'écriture sur la console.

Copiez et collez la méthode **WriteToConsoleAndPromptToContinue** sous la méthode **GetStartedDemo**.

```csharp
// ADD THIS PART TO YOUR CODE
private void WriteToConsoleAndPromptToContinue(string format, params object[] args)
{
        Console.WriteLine(format, args);
        Console.WriteLine("Press any key to continue ...");
        Console.ReadKey();
}
```

Pour créer votre [base de données](sql-api-resources.md#databases) Azure Cosmos DB, utilisez la méthode [CreateDatabaseAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdatabaseasync.aspx) de la classe **DocumentClient**. Une base de données est le conteneur logique de stockage de documents JSON partitionné entre les collections.

Copiez et collez le code suivant dans votre méthode **GetStartedDemo** en dessous de la création du client. Cela va créer la base de données *FamilyDB*.

```csharp
private async Task GetStartedDemo()
{
    this.client = new DocumentClient(new Uri(EndpointUri), PrimaryKey);

    // ADD THIS PART TO YOUR CODE
    await this.client.CreateDatabaseIfNotExistsAsync(new Database { Id = "FamilyDB_oa" });
```

Appuyez sur le bouton **DocumentDBGettingStarted** pour exécuter l’application.

Félicitations ! Vous avez créé une base de données Azure Cosmos DB.  

## <a id="CreateColl"></a>Étape 5 : créer une collection
> [!WARNING]
> **CreateDocumentCollectionAsync** crée une collection avec un débit réservé, ce qui a des conséquences sur la tarification. Pour plus d'informations, visitez notre [page de tarification](https://azure.microsoft.com/pricing/details/cosmos-db/).

Vous pouvez créer une [collection](sql-api-resources.md#collections) à l’aide de la méthode [CreateDocumentCollectionAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentcollectionasync.aspx) de la classe **DocumentClient**. Une collection est un conteneur de documents JSON. Elle est associée à une logique d'application JavaScript.

Copiez et collez le code suivant dans votre méthode **GetStartedDemo** en dessous de la création de la base de données. Cela va créer la collection de documents *FamilyCollection_oa*.

```csharp
    this.client = new DocumentClient(new Uri(EndpointUri), PrimaryKey);

    await this.client.CreateDatabaseIfNotExists("FamilyDB_oa");

    // ADD THIS PART TO YOUR CODE
    await this.client.CreateDocumentCollectionIfNotExistsAsync(UriFactory.CreateDatabaseUri("FamilyDB_oa"), new DocumentCollection { Id = "FamilyCollection_oa" });
```

Appuyez sur le bouton **DocumentDBGettingStarted** pour exécuter l’application.

Félicitations ! Vous avez créé une collection de documents Azure Cosmos DB.  

## <a id="CreateDoc"></a>Étape 6 : Création de documents JSON
Vous pouvez créer un [document](sql-api-resources.md#documents) à l’aide de la méthode [CreateDocumentAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentasync.aspx) de la classe **DocumentClient**. Les documents sont du contenu JSON (arbitraire) défini par l'utilisateur. Nous pouvons maintenant insérer un ou plusieurs documents. Si vous disposez déjà de données que vous souhaitez stocker dans votre base de données, vous pouvez utiliser [l’outil de migration de données](import-data.md) d’Azure Cosmos DB.

Nous devons tout d’abord créer une classe **Family** représentant les objets stockés dans Azure Cosmos DB dans cet exemple. Nous allons également créer les sous-classes **Parent**, **Child**, **Pet** et **Address** qui seront utilisées dans **Family**. Notez que les documents doivent avoir une propriété **Id** sérialisée comme **id** dans JSON. Créez ces classes en ajoutant les sous-classes internes suivantes après la méthode **GetStartedDemo** .

Copiez et collez les classes **Family**, **Parent**, **Child**, **Pet** et **Address** sous la méthode **WriteToConsoleAndPromptToContinue**.

```csharp
private void WriteToConsoleAndPromptToContinue(string format, params object[] args)
{
    Console.WriteLine(format, args);
    Console.WriteLine("Press any key to continue ...");
    Console.ReadKey();
}

// ADD THIS PART TO YOUR CODE
public class Family
{
    [JsonProperty(PropertyName = "id")]
    public string Id { get; set; }
    public string LastName { get; set; }
    public Parent[] Parents { get; set; }
    public Child[] Children { get; set; }
    public Address Address { get; set; }
    public bool IsRegistered { get; set; }
    public override string ToString()
    {
            return JsonConvert.SerializeObject(this);
    }
}

public class Parent
{
    public string FamilyName { get; set; }
    public string FirstName { get; set; }
}

public class Child
{
    public string FamilyName { get; set; }
    public string FirstName { get; set; }
    public string Gender { get; set; }
    public int Grade { get; set; }
    public Pet[] Pets { get; set; }
}

public class Pet
{
    public string GivenName { get; set; }
}

public class Address
{
    public string State { get; set; }
    public string County { get; set; }
    public string City { get; set; }
}
```

Copiez et collez la méthode **CreateFamilyDocumentIfNotExists** sous votre méthode **CreateDocumentCollectionIfNotExists**.

```csharp
// ADD THIS PART TO YOUR CODE
private async Task CreateFamilyDocumentIfNotExists(string databaseName, string collectionName, Family family)
{
    try
    {
        await this.client.ReadDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, family.Id));
        this.WriteToConsoleAndPromptToContinue("Found {0}", family.Id);
    }
    catch (DocumentClientException de)
    {
        if (de.StatusCode == HttpStatusCode.NotFound)
        {
            await this.client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri(databaseName, collectionName), family);
            this.WriteToConsoleAndPromptToContinue("Created Family {0}", family.Id);
        }
        else
        {
            throw;
        }
    }
}
```

Insérez ensuite deux documents, un pour la famille Andersen, l’autre pour la famille Wakefield.

Copiez et collez le code suivant `// ADD THIS PART TO YOUR CODE` dans votre méthode **GetStartedDemo** en dessous de la création de la collection de documents.

```csharp
await this.CreateDatabaseIfNotExists("FamilyDB_oa");

await this.CreateDocumentCollectionIfNotExists("FamilyDB_oa", "FamilyCollection_oa");

// ADD THIS PART TO YOUR CODE
Family andersenFamily = new Family
{
        Id = "Andersen.1",
        LastName = "Andersen",
        Parents = new Parent[]
        {
                new Parent { FirstName = "Thomas" },
                new Parent { FirstName = "Mary Kay" }
        },
        Children = new Child[]
        {
                new Child
                {
                        FirstName = "Henriette Thaulow",
                        Gender = "female",
                        Grade = 5,
                        Pets = new Pet[]
                        {
                                new Pet { GivenName = "Fluffy" }
                        }
                }
        },
        Address = new Address { State = "WA", County = "King", City = "Seattle" },
        IsRegistered = true
};

await this.CreateFamilyDocumentIfNotExists("FamilyDB_oa", "FamilyCollection_oa", andersenFamily);

Family wakefieldFamily = new Family
{
        Id = "Wakefield.7",
        LastName = "Wakefield",
        Parents = new Parent[]
        {
                new Parent { FamilyName = "Wakefield", FirstName = "Robin" },
                new Parent { FamilyName = "Miller", FirstName = "Ben" }
        },
        Children = new Child[]
        {
                new Child
                {
                        FamilyName = "Merriam",
                        FirstName = "Jesse",
                        Gender = "female",
                        Grade = 8,
                        Pets = new Pet[]
                        {
                                new Pet { GivenName = "Goofy" },
                                new Pet { GivenName = "Shadow" }
                        }
                },
                new Child
                {
                        FamilyName = "Miller",
                        FirstName = "Lisa",
                        Gender = "female",
                        Grade = 1
                }
        },
        Address = new Address { State = "NY", County = "Manhattan", City = "NY" },
        IsRegistered = false
};

await this.CreateFamilyDocumentIfNotExists("FamilyDB_oa", "FamilyCollection_oa", wakefieldFamily);
```

Appuyez sur le bouton **DocumentDBGettingStarted** pour exécuter l’application.

Félicitations ! Vous avez créé deux documents Azure Cosmos DB.  

![Diagramme illustrant la relation hiérarchique existant entre le compte, la base de données en ligne, la collection et les documents utilisés par le didacticiel NoSQL pour créer une application de console C#](./media/sql-api-dotnetcore-get-started/nosql-tutorial-account-database.png)

## <a id="Query"></a>Étape 7 : interroger les ressources Azure Cosmos DB
Azure Cosmos DB prend en charge les [requêtes](sql-api-sql-query.md) enrichies sur les documents JSON stockés dans chaque collection.  L’exemple de code suivant affiche différentes requêtes (à l’aide de la syntaxe SQL d’Azure Cosmos DB et de LINQ) que nous pouvons exécuter sur les documents insérés à l’étape précédente.

Copiez et collez la méthode **ExecuteSimpleQuery** sous votre méthode **CreateFamilyDocumentIfNotExists**.

```csharp
// ADD THIS PART TO YOUR CODE
private void ExecuteSimpleQuery(string databaseName, string collectionName)
{
    // Set some common query options
    FeedOptions queryOptions = new FeedOptions { MaxItemCount = -1 };

        // Here we find the Andersen family via its LastName
        IQueryable<Family> familyQuery = this.client.CreateDocumentQuery<Family>(
                UriFactory.CreateDocumentCollectionUri(databaseName, collectionName), queryOptions)
                .Where(f => f.LastName == "Andersen");

        // The query is executed synchronously here, but can also be executed asynchronously via the IDocumentQuery<T> interface
        Console.WriteLine("Running LINQ query...");
        foreach (Family family in familyQuery)
        {
            Console.WriteLine("\tRead {0}", family);
        }

        // Now execute the same query via direct SQL
        IQueryable<Family> familyQueryInSql = this.client.CreateDocumentQuery<Family>(
                UriFactory.CreateDocumentCollectionUri(databaseName, collectionName),
                "SELECT * FROM Family WHERE Family.LastName = 'Andersen'",
                queryOptions);

        Console.WriteLine("Running direct SQL query...");
        foreach (Family family in familyQueryInSql)
        {
                Console.WriteLine("\tRead {0}", family);
        }

        Console.WriteLine("Press any key to continue ...");
        Console.ReadKey();
}
```

Copiez et collez le code suivant dans votre méthode **GetStartedDemo** en dessous de la création du deuxième document.

```csharp
await this.CreateFamilyDocumentIfNotExists("FamilyDB_oa", "FamilyCollection_oa", wakefieldFamily);

// ADD THIS PART TO YOUR CODE
this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");
```

Appuyez sur le bouton **DocumentDBGettingStarted** pour exécuter l’application.

Félicitations ! Vous avez interrogé une collection Azure Cosmos DB.

Le schéma suivant montre comment la syntaxe de requête SQL d’Azure Cosmos DB est appelée sur la collection que vous avez créée. C’est par ailleurs cette même logique qui s’applique à la requête LINQ.

![Diagramme illustrant l’étendue et la signification de la requête utilisée par le didacticiel NoSQL pour créer une application de console C#](./media/sql-api-dotnetcore-get-started/nosql-tutorial-collection-documents.png)

Le mot clé [FROM](sql-api-sql-query.md#FromClause) est facultatif dans la requête, car les requêtes Azure Cosmos DB sont déjà étendues à une collection unique. Par conséquent, « FROM Families f » peut être remplacé par «FROM root r » ou par tout autre nom de variable que vous choisissez. Azure Cosmos DB déduit alors que Families, root, ou le nom de variable choisi, référencent par défaut la collection actuelle.

## <a id="ReplaceDocument"></a>Étape 8 : remplacer le document JSON
Azure Cosmos DB prend en charge le remplacement des documents JSON.  

Copiez et collez la méthode **ReplaceFamilyDocument** sous votre méthode **ExecuteSimpleQuery**.

```csharp
// ADD THIS PART TO YOUR CODE
private async Task ReplaceFamilyDocument(string databaseName, string collectionName, string familyName, Family updatedFamily)
{
    try
    {
        await this.client.ReplaceDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, familyName), updatedFamily);
        this.WriteToConsoleAndPromptToContinue("Replaced Family {0}", familyName);
    }
    catch (DocumentClientException de)
    {
        throw;
    }
}
```

Copiez et collez le code suivant dans votre méthode **GetStartedDemo** en dessous de l’exécution de la requête. Après avoir remplacé le document, cela exécutera à nouveau la même requête pour afficher le document modifié.

```csharp
await this.CreateFamilyDocumentIfNotExists("FamilyDB_oa", "FamilyCollection_oa", wakefieldFamily);

this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");

// ADD THIS PART TO YOUR CODE
// Update the Grade of the Andersen Family child
andersenFamily.Children[0].Grade = 6;

await this.ReplaceFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1", andersenFamily);

this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");
```

Appuyez sur le bouton **DocumentDBGettingStarted** pour exécuter l’application.

Félicitations ! Vous avez remplacé un document Azure Cosmos DB.

## <a id="DeleteDocument"></a>Étape 9 : supprimer le document JSON
Azure Cosmos DB prend en charge la suppression des documents JSON.  

Copiez et collez la méthode **DeleteFamilyDocument** sous votre méthode **ReplaceFamilyDocument**.

```csharp
// ADD THIS PART TO YOUR CODE
private async Task DeleteFamilyDocument(string databaseName, string collectionName, string documentName)
{
    try
    {
        await this.client.DeleteDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, documentName));
        Console.WriteLine("Deleted Family {0}", documentName);
    }
    catch (DocumentClientException de)
    {
        throw;
    }
}
```

Copiez et collez le code suivant dans votre méthode **GetStartedDemo** en dessous de l’exécution de la deuxième requête.

```csharp
await this.ReplaceFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1", andersenFamily);

this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");

// ADD THIS PART TO CODE
await this.DeleteFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1");
```

Appuyez sur le bouton **DocumentDBGettingStarted** pour exécuter l’application.

Félicitations ! Vous avez supprimé un document Azure Cosmos DB.

## <a id="DeleteDatabase"></a>Étape 10 : supprimer la base de données
Supprimer la base de données créée revient à supprimer la base de données et toutes les ressources enfants (collections, documents, etc.).

Copiez et collez le code suivant dans votre méthode **GetStartedDemo** sous la suppression du document, pour supprimer la base de données et toutes ses ressources enfants.

```csharp
this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");

await this.DeleteFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1");

// ADD THIS PART TO CODE
// Clean up/delete the database
await this.client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri("FamilyDB_oa"));
```

Appuyez sur le bouton **DocumentDBGettingStarted** pour exécuter l’application.

Félicitations ! Vous avez supprimé une base de données Azure Cosmos DB.

## <a id="Run"></a>Étape 11 : exécuter votre application de console C#
Appuyez sur le bouton **DocumentDBGettingStarted** dans Visual Studio pour générer l’application en mode débogage.

La sortie de votre application de prise en main doit s’afficher dans la fenêtre de la console. Celle-ci doit présenter les résultats des requêtes que nous avons ajoutées, qui doivent correspondre au texte d'exemple ci-dessous.

```
Created FamilyDB_oa
Press any key to continue ...
Created FamilyCollection_oa
Press any key to continue ...
Created Family Andersen.1
Press any key to continue ...
Created Family Wakefield.7
Press any key to continue ...
Running LINQ query...
    Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Running direct SQL query...
    Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Replaced Family Andersen.1
Press any key to continue ...
Running LINQ query...
    Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":6,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Running direct SQL query...
    Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":6,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Deleted Family Andersen.1
End of demo, press any key to exit.
```

Félicitations ! Vous avez terminé le didacticiel et vous disposez d’une application de console C# qui fonctionne !

## <a id="GetSolution"></a> Obtenir la solution complète du didacticiel
Pour générer la solution GetStarted qui contient tous les exemples de cet article, vous devez avoir les éléments suivants :

* Un compte Azure actif. Si vous n’en avez pas, vous pouvez vous inscrire pour bénéficier d’un [compte gratuit](https://azure.microsoft.com/free/).
* Un [compte Azure Cosmos DB][create-sql-api-dotnet.md#create-account].
* La solution [GetStarted](https://github.com/Azure-Samples/documentdb-dotnet-core-getting-started) disponible sur GitHub.

Pour restaurer les références à l’API SQL pour le SDK .NET Core Azure Cosmos DB dans Visual Studio, cliquez avec le bouton droit sur la solution **GetStarted** dans l’Explorateur de solutions, puis cliquez sur **Activer la restauration des packages NuGet**. Ensuite, dans le fichier Program.cs, mettez à jour les valeurs pour EndpointUrl et AuthorizationKey comme décrit dans [Se connecter à un compte Azure Cosmos DB](#Connect).

## <a name="next-steps"></a>Étapes suivantes
* Vous voulez un didacticiel ASP.NET MVC plus complexe ? Reportez-vous à la page [Didacticiel ASP.NET MVC : développement d’applications web avec Azure Cosmos DB](sql-api-dotnet-application.md).
* Vous voulez développer une application Xamarin iOS, Android ou Forms à l’aide de l’API SQL pour le SDK .NET Core Azure Cosmos DB ? Consultez la page [Créer des applications mobiles avec Xamarin et Azure Cosmos DB](mobile-apps-with-xamarin.md).
* Vous voulez effectuer un test des performances et de la mise à l’échelle avec Azure Cosmos DB ? Consultez la page [Test des performances et de la mise à l’échelle avec Azure Cosmos DB](performance-testing.md).
* Découvrez comment [Surveiller le stockage, l’utilisation et les demandes Azure Cosmos DB](monitor-accounts.md).
* Exécutez des requêtes sur notre exemple de dataset dans le [Query Playground](https://www.documentdb.com/sql/demo).
* Pour en savoir plus sur le modèle de programmation, consultez la page [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

[create-sql-api-dotnet.md#create-account]: create-sql-api-dotnet.md#create-account
[keys]: media/sql-api-dotnetcore-get-started/nosql-tutorial-keys.png
