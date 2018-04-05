---
title: Guide pratique pour bien démarrer avec Stockage Table et les services connectés de Visual Studio (ASP.NET Core) | Microsoft Docs
description: Guide pratique pour bien démarrer avec Stockage Table d’Azure dans un projet ASP.NET Core dans Visual Studio après s’être connecté à un compte de stockage à l’aide des services connectés de Visual Studio
services: storage
documentationcenter: ''
author: ghogen
manager: douge
editor: ''
ms.assetid: c3c451d1-71ff-4222-a348-c41c98a02b85
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: ghogen
ms.openlocfilehash: c20176b33962760560bbdb1cfe0d41377227d720
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-get-started-with-azure-table-storage-and-visual-studio-connected-services"></a>Mise en route avec le stockage de tables Azure et les appareils connectés Visual Studio

[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

Cet article explique comment bien démarrer avec Stockage Table d’Azure dans Visual Studio après avoir créé ou référencé un compte de stockage Azure dans un projet ASP.NET Core à l'aide de la fonctionnalité **Services connectés** de Visual Studio. L’opération **Services connectés** installe les packages NuGet appropriés pour accéder au stockage Azure de votre projet et ajoute la chaîne de connexion pour le compte de stockage aux fichiers de configuration de votre projet. (Pour des informations générales sur Azure Storage, consultez la [documentation relative au stockage](https://azure.microsoft.com/documentation/services/storage/).)

Le service de stockage de tables Azure vous permet de stocker de grandes quantités de données structurées. Il s'agit d'une banque de données NoSQL qui accepte les appels authentifiés provenant de l'intérieur et de l'extérieur du cloud Azure. Les tables Azure sont idéales pour le stockage des données structurées non relationnelles. Pour obtenir des informations plus générales sur l’utilisation d’Azure Table Storage, consultez la page [Prise en main du stockage de tables Azure à l’aide de .NET](../storage/storage-dotnet-how-to-use-tables.md).

Pour commencer, vous devez d'abord créer une table dans votre compte de stockage. Cet article montre ensuite comment créer une table en C# et comment effectuer des opérations de table de base comme ajouter, modifier, lire et supprimer les entrées d'une table.  Le code utilise la Bibliothèque cliente Azure Storage pour .NET. Pour plus d’informations sur ASP.NET, voir le site [ASP.NET](http://www.asp.net)(en anglais).

Certaines des API Azure Storage sont asynchrones, et le code dans cet article suppose l'utilisation de méthodes asynchrones. Voir [Programmation asynchrone](https://docs.microsoft.com/dotnet/csharp/async) pour plus d'informations.

## <a name="access-tables-in-code"></a>Accès aux tables dans le code

Pour accéder aux tables dans les projets ASP.NET Core, vous devez inclure les éléments suivants aux fichiers sources C# qui accèdent à Stockage Table d’Azure.

1. Ajoutez les instructions `using` requises :

    ```cs
    using Microsoft.Framework.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Table;
    using System.Threading.Tasks;
    using LogLevel = Microsoft.Framework.Logging.LogLevel;
    ```

1. Obtenez un objet `CloudStorageAccount` qui représente les informations de votre compte de stockage. Le code suivant permet d'obtenir la chaîne de connexion de stockage et les informations de compte de stockage à partir de la configuration du service Azure :

    ```cs
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Obtenez un objet `CloudTableClient` pour référencer les objets de table de votre compte de stockage :

    ```cs
    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Obtenez un objet de référence `CloudTable` pour référencer une table et des entités spécifiques :

    ```cs
    // Get a reference to a table named "peopleTable"
    CloudTable peopleTable = tableClient.GetTableReference("peopleTable");
    ```

## <a name="create-a-table-in-code"></a>Création d'une table dans le code

Pour créer la table Azure, appelez CreateIfNotExistsAsync() :

```cs
// Create the CloudTable if it does not exist
await peopleTable.CreateIfNotExistsAsync();
```

## <a name="add-an-entity-to-a-table"></a>Ajout d'une entité à une table

Pour ajouter une entité à une table, commencez par créer une classe définissant les propriétés de votre entité. Le code suivant définit une classe d’entité appelée `CustomerEntity` qui utilise le prénom du client en tant que clé de ligne et son nom de famille en tant que clé de partition.

```cs
public class CustomerEntity : TableEntity
{
    public CustomerEntity(string lastName, string firstName)
    {
        this.PartitionKey = lastName;
        this.RowKey = firstName;
    }

    public CustomerEntity() { }

    public string Email { get; set; }

    public string PhoneNumber { get; set; }
}
```

Les opérations de table impliquant des entités utilisent l’objet `CloudTable` que vous avez créé précédemment dans la section [ Accès aux tables dans le code](#access-tables-in-code). L'objet `TableOperation` représente l'opération à effectuer. L’exemple de code suivant montre comment créer des objets `CloudTable` et `CustomerEntity`. Pour préparer l’opération, un objet `TableOperation` est créé pour insérer l’entité du client dans la table. Finalement, l’opération est exécutée en appelant `CloudTable.ExecuteAsync`.

```cs
// Create a new customer entity.
CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
customer1.Email = "Walter@contoso.com";
customer1.PhoneNumber = "425-555-0101";

// Create the TableOperation that inserts the customer entity.
TableOperation insertOperation = TableOperation.Insert(customer1);

// Execute the insert operation.
await peopleTable.ExecuteAsync(insertOperation);
```

## <a name="insert-a-batch-of-entities"></a>Insertion d'un lot d'entités

Vous pouvez insérer plusieurs entités dans une table en une seule opération d'écriture. L’exemple de code suivant crée deux objets d’entité (« Jeff Smith » et « Ben Smith »), les ajoute à un objet `TableBatchOperation` en utilisant la méthode `Insert`, puis démarre l’opération en appelant `CloudTable.ExecuteBatchAsync`.

```cs
// Create the batch operation.
TableBatchOperation batchOperation = new TableBatchOperation();

// Create a customer entity and add it to the table.
CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
customer1.Email = "Jeff@contoso.com";
customer1.PhoneNumber = "425-555-0104";

// Create another customer entity and add it to the table.
CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
customer2.Email = "Ben@contoso.com";
customer2.PhoneNumber = "425-555-0102";

// Add both customer entities to the batch insert operation.
batchOperation.Insert(customer1);
batchOperation.Insert(customer2);

// Execute the batch operation.
await peopleTable.ExecuteBatchAsync(batchOperation);
```

## <a name="get-all-of-the-entities-in-a-partition"></a>Recherche de toutes les entités d'une partition

Pour exécuter une requête de table portant sur toutes les entités d'une partition, utilisez un objet `TableQuery`. L’exemple de code suivant indique un filtre pour les entités où ’Smith’ est la clé de partition. Il imprime les champs de chaque entité dans les résultats de requête vers la console.

```cs
// Construct the query operation for all customer entities where PartitionKey="Smith".
TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

// Print the fields for each customer.
TableContinuationToken token = null;
do
{
    TableQuerySegment<CustomerEntity> resultSegment = await peopleTable.ExecuteQuerySegmentedAsync(query, token);
    token = resultSegment.ContinuationToken;

    foreach (CustomerEntity entity in resultSegment.Results)
    {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
        entity.Email, entity.PhoneNumber);
    }
} while (token != null);
```

## <a name="get-a-single-entity"></a>Obtention d'une seule entité

Vous pouvez écrire une requête pour obtenir une seule entité. Le code suivant utilise un objet `TableOperation` pour spécifier le client « Ben Smith ». La méthode renvoie une seule entité, au lieu d’une collection. De plus, la valeur renvoyée dans `TableResult.Result` et `CustomerEntity`. La méthode la plus rapide pour extraire une seule entité dans le service `Table` consiste à spécifier une clé de partition et une clé de ligne.

```cs
// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the retrieve operation.
TableResult retrievedResult = await peopleTable.ExecuteAsync(retrieveOperation);

// Print the phone number of the result.
if (retrievedResult.Result != null)
   Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
else
   Console.WriteLine("The phone number could not be retrieved.");
```

## <a name="delete-an-entity"></a>Suppression d’une entité

Une fois l'entité trouvée, vous pouvez la supprimer. Le code suivant recherche puis supprime l'entité de client « Ben Smith ».

```cs
// Create a retrieve operation that expects a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the operation.
TableResult retrievedResult = peopleTable.Execute(retrieveOperation);

// Assign the result to a CustomerEntity object.
CustomerEntity deleteEntity = (CustomerEntity)retrievedResult.Result;

// Create the Delete TableOperation and then execute it.
if (deleteEntity != null)
{
   TableOperation deleteOperation = TableOperation.Delete(deleteEntity);

   // Execute the operation.
   await peopleTable.ExecuteAsync(deleteOperation);

   Console.WriteLine("Entity deleted.");
}

else
   Console.WriteLine("Couldn't delete the entity.");
```

## <a name="next-steps"></a>Étapes suivantes
[!INCLUDE [vs-storage-dotnet-tables-next-steps](../../includes/vs-storage-dotnet-tables-next-steps.md)]
