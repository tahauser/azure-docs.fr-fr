---
title: Liaisons Azure Cosmos DB pour Azure Functions
description: "Découvrez comment utiliser des déclencheurs et liaisons Azure Cosmos DB dans Azure Functions."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: "azure functions, fonctions, traitement des événements, calcul dynamique, architecture sans serveur"
ms.service: functions; cosmos-db
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/21/2017
ms.author: glenga
ms.openlocfilehash: 1a57d26e0f1188a2dea29beba52fde090aa82ca8
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/24/2018
---
# <a name="azure-cosmos-db-bindings-for-azure-functions"></a>Liaisons Azure Cosmos DB pour Azure Functions

Cet article explique comment utiliser des liaisons [Azure Cosmos DB](..\cosmos-db\serverless-computing-database.md) dans Azure Functions. Azure Functions prend en charge les liaisons de déclencheur, d’entrée et de sortie pour Azure Cosmos DB.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="trigger"></a>Déclencheur

Le déclencheur Azure Cosmos DB utilise le [flux de modification d’Azure Cosmos DB](../cosmos-db/change-feed.md) pour écouter les modifications sur plusieurs partitions. Le flux de modification publie les insertions et mises à jour, pas les suppressions. 

## <a name="trigger---example"></a>Déclencheur - exemple

Consultez l’exemple propre à un langage particulier :

* [C#](#trigger---c-example)
* [Script C# (.csx)](#trigger---c-script-example)
* [JavaScript](#trigger---javascript-example)

### <a name="trigger---c-example"></a>Déclencheur - exemple C#

L’exemple suivant montre une [fonction C#](functions-dotnet-class-library.md) qui se déclenche à partir d’une base de données et d’une collection spécifiques.

```cs
    using System.Collections.Generic;
    using Microsoft.Azure.Documents;
    using Microsoft.Azure.WebJobs;
    using Microsoft.Azure.WebJobs.Host;

    [FunctionName("DocumentUpdates")]
    public static void Run(
        [CosmosDBTrigger("database", "collection", ConnectionStringSetting = "myCosmosDB")]
    IReadOnlyList<Document> documents,
        TraceWriter log)
    {
            log.Info("Documents modified " + documents.Count);
            log.Info("First document Id " + documents[0].Id);
    }
```

### <a name="trigger---c-script-example"></a>Déclencheur - exemple Script C#

L’exemple suivant montre une liaison de déclencheur Cosmos DB dans un fichier *function.json* et une [fonction de script C#](functions-reference-csharp.md) qui utilise la liaison. La fonction écrit des messages de journal quand des enregistrements Cosmos DB sont modifiés.

Voici les données de liaison dans le fichier *function.json* :

```json
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

Voici le code Script C# :
 
```cs 
    #r "Microsoft.Azure.Documents.Client"
    
    using System;
    using Microsoft.Azure.Documents;
    using System.Collections.Generic;
    

    public static void Run(IReadOnlyList<Document> documents, TraceWriter log)
    {
      log.Verbose("Documents modified " + documents.Count);
      log.Verbose("First document Id " + documents[0].Id);
    }
```

### <a name="trigger---javascript-example"></a>Déclencheur - exemple JavaScript

L’exemple suivant montre une liaison de déclencheur Cosmos DB dans un fichier *function.json* et une [fonction JavaScript](functions-reference-node.md) qui utilise la liaison. La fonction écrit des messages de journal quand des enregistrements Cosmos DB sont modifiés.

Voici les données de liaison dans le fichier *function.json* :

```json
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

Voici le code JavaScript :

```javascript
    module.exports = function (context, documents) {
      context.log('First document Id modified : ', documents[0].id);

      context.done();
    }
```

## <a name="trigger---attributes"></a>Déclencheur - attributs

Dans les [bibliothèques de classes C#](functions-dotnet-class-library.md), utilisez l’attribut [CosmosDBTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/Trigger/CosmosDBTriggerAttribute.cs), qui est défini dans le package NuGet [Microsoft.Azure.WebJobs.Extensions.CosmosDB](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB).

Le constructeur de l’attribut accepte le nom de la base de données et le nom de la collection. Pour plus d’informations sur ces paramètres et d’autres propriétés que vous pouvez configurer, consultez [Déclencheur - configuration](#trigger---configuration). Voici un exemple d’attribut `CosmosDBTrigger` dans une signature de méthode :

```csharp
    [FunctionName("DocumentUpdates")]
    public static void Run(
        [CosmosDBTrigger("database", "collection", ConnectionStringSetting = "myCosmosDB")]
    IReadOnlyList<Document> documents,
        TraceWriter log)
    {
        ...
    }
```

Pour obtenir un exemple complet, consultez [Déclencheur - exemple C#](#trigger---c-example).

## <a name="trigger---configuration"></a>Déclencheur - configuration

Le tableau suivant décrit les propriétés de configuration de liaison que vous définissez dans le fichier *function.json* et l’attribut `CosmosDBTrigger`.

|Propriété function.json | Propriété d’attribut |Description|
|---------|---------|----------------------|
|**type** || Cette propriété doit être définie sur `cosmosDBTrigger`. |
|**direction** || Cette propriété doit être définie sur `in`. Ce paramètre est défini automatiquement lorsque vous créez le déclencheur dans le portail Azure. |
|**name** || Nom de variable utilisé dans le code de fonction, qui représente la liste des documents modifiés. | 
|**connectionStringSetting**|**ConnectionStringSetting** | Nom d’un paramètre d’application contenant la chaîne de connexion utilisée pour se connecter au compte Azure Cosmos DB surveillé. |
|**databaseName**|**DatabaseName**  | Nom de la base de données Azure Cosmos DB contenant la collection surveillée. |
|**collectionName** |**CollectionName** | Nom de la collection surveillée. |
|**leaseConnectionStringSetting** | **LeaseConnectionStringSetting** | (Facultatif) Nom d’un paramètre d’application contenant la chaîne de connexion au service stockant la collection de baux. S’il n’est pas défini, la valeur `connectionStringSetting` est utilisée. Ce paramètre est automatiquement défini lorsque la liaison est créée dans le portail. La chaîne de connexion de la collection de baux doit avoir des autorisations en écriture.|
|**leaseDatabaseName** |**LeaseDatabaseName** | (Facultatif) Nom de la base de données contenant la collection utilisée pour stocker des baux. S’il n’est pas défini, la valeur du paramètre `databaseName` est utilisée. Ce paramètre est automatiquement défini lorsque la liaison est créée dans le portail. |
|**leaseCollectionName** | **LeaseCollectionName** | (Facultatif) Nom de la collection utilisée pour stocker des baux. S’il n’est pas défini, la valeur `leases` est utilisée. |
|**createLeaseCollectionIfNotExists** | **CreateLeaseCollectionIfNotExists** | (Facultatif) Lorsque la valeur est définie sur `true`, la collection de baux est créée automatiquement si elle n’existe pas. La valeur par défaut est `false`. |
|**leasesCollectionThroughput**| **LeasesCollectionThroughput**| (Facultatif) Définit la quantité d’unités de requête à attribuer lors de la création de la collection de baux. Ce paramètre est utilisé uniquement quand `createLeaseCollectionIfNotExists` est défini sur `true`. Ce paramètre est automatiquement défini lors de la création de la liaison à l’aide du portail.
| |**LeaseOptions** | Configurez les options de bail en définissant des propriétés dans une instance de la classe [ChangeFeedHostOptions](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.changefeedprocessor.changefeedhostoptions).

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Déclencheur - utilisation

Le déclencheur nécessite une deuxième collection qu’il utilise pour stocker des _baux_ sur les partitions. La collection surveillée et la collection contenant les baux doivent être disponibles pour que le déclencheur fonctionne.

 >[!IMPORTANT]
 > Si plusieurs fonctions sont configurées pour utiliser un déclencheur Cosmos DB pour la même collection, chacune de ces fonctions doit utiliser une collection de bail dédiée. Sinon, une seule des fonctions est déclenchée. 

Le déclencheur n’indique pas si un document a été mis à jour ou inséré, il fournit simplement le document lui-même. Si vous avez besoin de gérer les mises à jour et insertions différemment, vous pouvez le faire en implémentant des champs d’horodatage pour l’insertion ou la mise à jour.

## <a name="input"></a>Entrée

La liaison d’entrée Azure Cosmos DB récupère un ou plusieurs documents Azure Cosmos DB et les transmet au paramètre d’entrée de la fonction. L’ID du document ou les paramètres de requête peuvent être déterminés en fonction du déclencheur qui appelle la fonction. 

>[!NOTE]
> N’utilisez pas des liaisons d’entrée ou de sortie Azure Cosmos DB si vous utilisez l’API MongoDB sur un compte Cosmos DB. L’altération des données est une possibilité.

## <a name="input---example-1"></a>Entrée - exemple 1

Consultez l’exemple propre au langage, qui lit un document unique :

* [C#](#input---c-example)
* [Script C# (.csx)](#input---c-script-example)
* [F#](#input---f-example)
* [JavaScript](#input---javascript-example)

### <a name="input---c-example"></a>Entrée - exemple C#

L’exemple suivant montre une [fonction C#](functions-dotnet-class-library.md) qui récupère un document unique depuis une base de données et une collection spécifiques. 

D’abord, les valeurs `Id` et `Maker` pour une instance `CarReview` sont passées dans la file d’attente. La liaison Cosmos DB utilise `Id` et `Maker` du message de file d’attente pour récupérer le document de la base de données.

```cs
    using Microsoft.Azure.WebJobs;
    using Microsoft.Azure.WebJobs.Host;

    namespace CosmosDB
    {
        public static class SingleEntry
        {
            [FunctionName("SingleEntry")]
            public static void Run(
                [QueueTrigger("car-reviews", Connection = "StorageConnectionString")] CarReview carReview,
                [CosmosDB("cars", "car-reviews", PartitionKey = "{maker}", Id= "{id}", ConnectionStringSetting = "CarReviewsConnectionString")] CarReview document,
                TraceWriter log)
            {
                log.Info( $"Selected Review - {document?.Review}"); 
            }
        }
    }
```

Voici l’OCT `CarReview` :

 ```cs
    public class CarReview
    {
        public string Id { get; set; }
        public string Maker { get; set; }
        public string Description { get; set; }
        public string Model { get; set; }
        public string Image { get; set; }
        public string Review { get; set; }
    }
 ```

### <a name="input---c-script-example"></a>Entrée - exemple de script C#

L’exemple suivant montre une liaison d’entrée Cosmos DB dans un fichier *function.json* et une [fonction de script C#](functions-reference-csharp.md) qui utilise la liaison. La fonction lit un document unique et met à jour la valeur texte du document.

Voici les données de liaison dans le fichier *function.json* :

```json
{
    "name": "inputDocument",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger}",
    "partitionKey": "{partition key value}",
    "connection": "MyAccount_COSMOSDB",     
    "direction": "in"
}
```
La section [configuration](#input---configuration) décrit ces propriétés.

Voici le code Script C# :

```cs
    using System;

    // Change input document contents using Azure Cosmos DB input binding 
    public static void Run(string myQueueItem, dynamic inputDocument)
    {   
      inputDocument.text = "This has changed.";
    }
```

<a name="infsharp"></a>

### <a name="input---f-example"></a>Entrée - exemple F#

L’exemple suivant montre une liaison d’entrée Cosmos DB dans un fichier *function.json* et une [fonction F#](functions-reference-fsharp.md) qui utilise la liaison. La fonction lit un document unique et met à jour la valeur texte du document.

Voici les données de liaison dans le fichier *function.json* :

```json
{
    "name": "inputDocument",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger}",
    "connection": "MyAccount_COSMOSDB",     
    "direction": "in"
}
```

La section [configuration](#input---configuration) décrit ces propriétés.

Voici le code F# :

```fsharp
    (* Change input document contents using Azure Cosmos DB input binding *)
    open FSharp.Interop.Dynamic
    let Run(myQueueItem: string, inputDocument: obj) =
    inputDocument?text <- "This has changed."
```

Cet exemple nécessite un fichier `project.json` qui spécifie les dépendances NuGet `FSharp.Interop.Dynamic` et `Dynamitey` :

```json
{
    "frameworks": {
        "net46": {
            "dependencies": {
                "Dynamitey": "1.0.2",
                "FSharp.Interop.Dynamic": "3.0.0"
            }
        }
    }
}
```

Pour ajouter un fichier `project.json`, consultez [F# package management](functions-reference-fsharp.md#package) (Gestion des packages F#).

### <a name="input---javascript-example"></a>Entrée - exemple JavaScript

L’exemple suivant montre une liaison d’entrée Cosmos DB dans un fichier *function.json* et une [fonction JavaScript](functions-reference-node.md) qui utilise la liaison. La fonction lit un document unique et met à jour la valeur texte du document.

Voici les données de liaison dans le fichier *function.json* :

```json
{
    "name": "inputDocument",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger_payload_property}",
    "partitionKey": "{queueTrigger_payload_property}",
    "connection": "MyAccount_COSMOSDB",     
    "direction": "in"
}
```
La section [configuration](#input---configuration) décrit ces propriétés.

Voici le code JavaScript :

```javascript
    // Change input document contents using Azure Cosmos DB input binding, using context.bindings.inputDocumentOut
    module.exports = function (context) {   
    context.bindings.inputDocumentOut = context.bindings.inputDocumentIn;
    context.bindings.inputDocumentOut.text = "This was updated!";
    context.done();
    };
```

## <a name="input---example-2"></a>Entrée - exemple 2

Consultez l’exemple propre au langage, qui lit plusieurs documents :

* [C#](#input---c-example-2)
* [Script C# (.csx)](#input---c-script-example-2)
* [JavaScript](#input---javascript-example-2)

### <a name="input---c-example-2"></a>Entrée - exemple 2 C#

L’exemple suivant montre une [fonction C#](functions-dotnet-class-library.md) qui exécute une requête SQL. Pour utiliser le paramètre `SqlQuery`, vous devez installer la dernière version bêta du package NuGet `Microsoft.Azure.WebJobs.Extensions.DocumentDB`.

```csharp
    using System.Net;
    using System.Net.Http;
    using System.Collections.Generic;
    using Microsoft.Azure.WebJobs;
    using Microsoft.Azure.WebJobs.Extensions.Http;

    [FunctionName("CosmosDBSample")]
    public static HttpResponseMessage Run(
        [HttpTrigger(AuthorizationLevel.Anonymous, "get")] HttpRequestMessage req,
        [CosmosDB("test", "test", ConnectionStringSetting = "CosmosDB", SqlQuery = "SELECT top 2 * FROM c order by c._ts desc")] IEnumerable<object> documents)
    {
        return req.CreateResponse(HttpStatusCode.OK, documents);
    }
```

### <a name="input---c-script-example-2"></a>Entrée - exemple 2 Script C#

L’exemple suivant montre une liaison d’entrée Azure Cosmos DB dans un fichier *function.json* et une [fonction de script C#](functions-reference-csharp.md) qui utilise la liaison. La fonction récupère plusieurs documents spécifiés par une requête SQL, à l’aide d’un déclencheur de file d’attente pour personnaliser les paramètres de requête.

Le déclencheur de file d’attente fournit un paramètre `departmentId`. Un message de file d’attente de `{ "departmentId" : "Finance" }` retourne tous les enregistrements du service financier. 

Voici les données de liaison dans le fichier *function.json* :

```json
{
    "name": "documents",
    "type": "documentdb",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connection": "CosmosDBConnection"
}
```

La section [configuration](#input---configuration) décrit ces propriétés.

Voici le code Script C# :

```csharp
    public static void Run(QueuePayload myQueueItem, IEnumerable<dynamic> documents)
    {   
        foreach (var doc in documents)
        {
            // operate on each document
        }    
    }

    public class QueuePayload
    {
        public string departmentId { get; set; }
    }
```

### <a name="input---javascript-example-2"></a>Entrée - exemple JavaScript 2

L’exemple suivant montre une liaison d’entrée Azure Cosmos DB dans un fichier *function.json* et une [fonction JavaScript](functions-reference-node.md) qui utilise la liaison. La fonction récupère plusieurs documents spécifiés par une requête SQL, à l’aide d’un déclencheur de file d’attente pour personnaliser les paramètres de requête.

Le déclencheur de file d’attente fournit un paramètre `departmentId`. Un message de file d’attente de `{ "departmentId" : "Finance" }` retourne tous les enregistrements du service financier. 

Voici les données de liaison dans le fichier *function.json* :

```json
{
    "name": "documents",
    "type": "documentdb",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connection": "CosmosDBConnection"
}
```

La section [configuration](#input---configuration) décrit ces propriétés.

Voici le code JavaScript :

```javascript
    module.exports = function (context, input) {    
        var documents = context.bindings.documents;
        for (var i = 0; i < documents.length; i++) {
            var document = documents[i];
            // operate on each document
        }       
        context.done();
    };
```

## <a name="input---attributes"></a>Entrée - attributs

Dans les [bibliothèques de classes C#](functions-dotnet-class-library.md), utilisez l’attribut [CosmosDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs), qui est défini dans le package NuGet [Microsoft.Azure.WebJobs.Extensions.CosmosDB](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB).

Le constructeur de l’attribut accepte le nom de la base de données et le nom de la collection. Pour plus d’informations sur ces paramètres et d’autres propriétés que vous pouvez configurer, consultez [la section de configuration suivante](#input---configuration). 

## <a name="input---configuration"></a>Entrée - configuration

Le tableau suivant décrit les propriétés de configuration de liaison que vous définissez dans le fichier *function.json* et l’attribut `CosmosDB`.

|Propriété function.json | Propriété d’attribut |Description|
|---------|---------|----------------------|
|**type**     || Cette propriété doit être définie sur `documentdb`.        |
|**direction**     || Cette propriété doit être définie sur `in`.         |
|**name**     || Nom du paramètre de liaison qui représente le document dans la fonction.  |
|**databaseName** |**DatabaseName** |Base de données contenant le document.        |
|**collectionName** |**CollectionName** | Nom de la collection qui contient le document. |
|**id**    | **Id** | ID du document à récupérer. Cette propriété prend en charge les paramètres de liaison. Pour plus d’informations, consultez [Lier aux propriétés d’entrée personnalisées dans une expression de liaison](functions-triggers-bindings.md#bind-to-custom-input-properties). Ne définissez pas à la fois la propriété **id** et la propriété **sqlQuery**. Si vous ne définissez aucune des deux, l’ensemble de la collection est récupéré. |
|**sqlQuery**  |**SqlQuery**  | Requête SQL Azure Cosmos DB utilisée pour récupérer plusieurs documents. La propriété prend en charge les liaisons d’exécution, comme dans cet exemple : `SELECT * FROM c where c.departmentId = {departmentId}`. Ne définissez pas à la fois la propriété **id** et la propriété **sqlQuery**. Si vous ne définissez aucune des deux, l’ensemble de la collection est récupéré.|
|**Connexion**     |**ConnectionStringSetting**|Nom du paramètre d’application contenant votre chaîne de connexion Azure Cosmos DB.        |
|**partitionKey**|**PartitionKey**|Spécifie la valeur de la clé de partition pour la recherche. Peut inclure des paramètres de liaison.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Entrée - utilisation

Dans les fonctions C# et F#, lorsque la fonction se termine correctement, toutes les modifications apportées au document d’entrée par le biais des paramètres d’entrée nommés sont automatiquement conservées. 

Dans les fonctions JavaScript, les mises à jour ne sont pas effectuées une fois la fonction terminée. Utilisez plutôt `context.bindings.<documentName>In` et `context.bindings.<documentName>Out` pour effectuer les mises à jour. Consultez [l’exemple JavaScript](#input---javascript-example).

## <a name="output"></a>Sortie

La liaison de sortie Azure Cosmos DB vous permet d’écrire un nouveau document dans une base de données Azure Cosmos DB. 

>[!NOTE]
> N’utilisez pas des liaisons d’entrée ou de sortie Azure Cosmos DB si vous utilisez l’API MongoDB sur un compte Cosmos DB. L’altération des données est une possibilité.

## <a name="output---example"></a>Sortie - exemple

Consultez l’exemple propre à un langage particulier :

* [C#](#output---c-example)
* [Script C# (.csx)](#output---c-script-example)
* [F#](#output---f-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>Sortie - exemple C#

L’exemple suivant montre une [fonction C#](functions-dotnet-class-library.md) qui ajoute un document à une base de données, à l’aide des données fournies dans le message de Stockage File d’attente.

```cs
    using System;
    using Microsoft.Azure.WebJobs;

    [FunctionName("QueueToDocDB")]        
    public static void Run(
        [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
        [CosmosDB("ToDoList", "Items", Id = "id", ConnectionStringSetting = "myCosmosDB")] out dynamic document)
    {
        document = new { Text = myQueueItem, id = Guid.NewGuid() };
    }
```

### <a name="output---c-script-example"></a>Sortie - exemple Script C#

L’exemple suivant montre une liaison de sortie Azure Cosmos DB dans un fichier *function.json* et une [fonction de script C#](functions-reference-csharp.md) qui utilise la liaison. La fonction utilise une liaison d’entrée de file d’attente pour une file d’attente qui reçoit le code JSON au format suivant :

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

La fonction crée des documents Azure Cosmos DB au format suivant pour chaque enregistrement :

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Voici les données de liaison dans le fichier *function.json* :

```json
{
    "name": "employeeDocument",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connection": "MyAccount_COSMOSDB",     
    "direction": "out"
}
```

La section [configuration](#output---configuration) décrit ces propriétés.

Voici le code Script C# :

```cs
    #r "Newtonsoft.Json"

    using Microsoft.Azure.WebJobs.Host;
    using Newtonsoft.Json.Linq;

    public static void Run(string myQueueItem, out object employeeDocument, TraceWriter log)
    {
      log.Info($"C# Queue trigger function processed: {myQueueItem}");

      dynamic employee = JObject.Parse(myQueueItem);

      employeeDocument = new {
        id = employee.name + "-" + employee.employeeId,
        name = employee.name,
        employeeId = employee.employeeId,
        address = employee.address
      };
    }
```

Pour créer plusieurs documents, vous pouvez vous lier à `ICollector<T>` ou `IAsyncCollector<T>` où `T` est un des types pris en charge.

### <a name="output---f-example"></a>Sortie - exemple F#

L’exemple suivant montre une liaison de sortie Azure Cosmos DB dans un fichier *function.json* et une [fonction F#](functions-reference-fsharp.md) qui utilise la liaison. La fonction utilise une liaison d’entrée de file d’attente pour une file d’attente qui reçoit le code JSON au format suivant :

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

La fonction crée des documents Azure Cosmos DB au format suivant pour chaque enregistrement :

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Voici les données de liaison dans le fichier *function.json* :

```json
{
    "name": "employeeDocument",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connection": "MyAccount_COSMOSDB",     
    "direction": "out"
}
```
La section [configuration](#output---configuration) décrit ces propriétés.

Voici le code F# :

```fsharp
    open FSharp.Interop.Dynamic
    open Newtonsoft.Json

    type Employee = {
      id: string
      name: string
      employeeId: string
      address: string
    }

    let Run(myQueueItem: string, employeeDocument: byref<obj>, log: TraceWriter) =
      log.Info(sprintf "F# Queue trigger function processed: %s" myQueueItem)
      let employee = JObject.Parse(myQueueItem)
      employeeDocument <-
        { id = sprintf "%s-%s" employee?name employee?employeeId
          name = employee?name
          employeeId = employee?employeeId
          address = employee?address }
```

Cet exemple nécessite un fichier `project.json` qui spécifie les dépendances NuGet `FSharp.Interop.Dynamic` et `Dynamitey` :

```json
{
    "frameworks": {
        "net46": {
          "dependencies": {
            "Dynamitey": "1.0.2",
            "FSharp.Interop.Dynamic": "3.0.0"
           }
        }
    }
}
```

Pour ajouter un fichier `project.json`, consultez [F# package management](functions-reference-fsharp.md#package) (Gestion des packages F#).

### <a name="output---javascript-example"></a>Sortie - exemple JavaScript

L’exemple suivant montre une liaison de sortie Azure Cosmos DB dans un fichier *function.json* et une [fonction JavaScript](functions-reference-node.md) qui utilise la liaison. La fonction utilise une liaison d’entrée de file d’attente pour une file d’attente qui reçoit le code JSON au format suivant :

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

La fonction crée des documents Azure Cosmos DB au format suivant pour chaque enregistrement :

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Voici les données de liaison dans le fichier *function.json* :

```json
{
    "name": "employeeDocument",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connection": "MyAccount_COSMOSDB",     
    "direction": "out"
}
```

La section [configuration](#output---configuration) décrit ces propriétés.

Voici le code JavaScript :

```javascript
    module.exports = function (context) {

      context.bindings.employeeDocument = JSON.stringify({ 
        id: context.bindings.myQueueItem.name + "-" + context.bindings.myQueueItem.employeeId,
        name: context.bindings.myQueueItem.name,
        employeeId: context.bindings.myQueueItem.employeeId,
        address: context.bindings.myQueueItem.address
      });

      context.done();
    };
```

## <a name="output---attributes"></a>Sortie - attributs

Dans les [bibliothèques de classes C#](functions-dotnet-class-library.md), utilisez l’attribut [CosmosDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs), qui est défini dans le package NuGet [Microsoft.Azure.WebJobs.Extensions.CosmosDB](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB).

Le constructeur de l’attribut accepte le nom de la base de données et le nom de la collection. Pour plus d’informations sur ces paramètres et d’autres propriétés que vous pouvez configurer, consultez [Sortie - configuration](#output---configuration). Voici un exemple d’attribut `CosmosDB` dans une signature de méthode :

```csharp
    [FunctionName("QueueToDocDB")]        
    public static void Run(
        [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
        [CosmosDB("ToDoList", "Items", Id = "id", ConnectionStringSetting = "myCosmosDB")] out dynamic document)
    {
        ...
    }
```

Pour obtenir un exemple complet, consultez [Sortie - exemple C#](#output---c-example).

## <a name="output---configuration"></a>Sortie - configuration

Le tableau suivant décrit les propriétés de configuration de liaison que vous définissez dans le fichier *function.json* et l’attribut `CosmosDB`.

|Propriété function.json | Propriété d’attribut |Description|
|---------|---------|----------------------|
|**type**     || Cette propriété doit être définie sur `documentdb`.        |
|**direction**     || Cette propriété doit être définie sur `out`.         |
|**name**     || Nom du paramètre de liaison qui représente le document dans la fonction.  |
|**databaseName** | **DatabaseName**|Base de données contenant la collection dans laquelle le document est créé.     |
|**collectionName** |**CollectionName**  | Nom de la collection dans laquelle le document est créé. |
|**createIfNotExists**  |**CreateIfNotExists**    | Valeur booléenne indiquant si la collection doit être créée si elle n’existe pas déjà. La valeur par défaut est *false* car les nouvelles collections sont créées avec un débit réservé, ce qui a des conséquences sur la tarification. Pour plus d’informations, consultez la [page relative aux prix appliqués](https://azure.microsoft.com/pricing/details/documentdb/).  |
|**partitionKey**|**PartitionKey** |Lorsque `CreateIfNotExists` a la valeur true, définit le chemin de la clé de partition pour la collection créée.|
|**collectionThroughput**|**CollectionThroughput**| Lorsque `CreateIfNotExists` a la valeur true, définit le [débit](../cosmos-db/set-throughput.md) de la collection créée.|
|**Connexion**    |**ConnectionStringSetting** |Nom du paramètre d’application contenant votre chaîne de connexion Azure Cosmos DB.        |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Sortie - utilisation

Par défaut, lorsque vous écrivez dans le paramètre de sortie de votre fonction, un document est créé dans votre base de données. Ce document comporte un GUID généré automatiquement en tant qu’ID du document. Vous pouvez spécifier l’ID du document de sortie en spécifiant la propriété `id` dans l’objet JSON qui est passé au paramètre de sortie. 

> [!Note]  
> Lorsque vous spécifier l’ID d’un document existant, il est remplacé par le nouveau document de sortie. 

## <a name="exceptions-and-return-codes"></a>Exceptions et codes de retour

| Liaison | Informations de référence |
|---|---|
| CosmosDB | [Codes d’erreur CosmosDB](https://docs.microsoft.com/en-us/rest/api/documentdb/http-status-codes-for-documentdb) |

## <a name="next-steps"></a>étapes suivantes

> [!div class="nextstepaction"]
> [Accéder à un guide de démarrage rapide qui utilise un déclencheur Cosmos DB](functions-create-cosmos-db-triggered-function.md)

> [!div class="nextstepaction"]
> [En savoir plus sur le traitement de base de données serverless avec Cosmos DB](..\cosmos-db\serverless-computing-database.md)

> [!div class="nextstepaction"]
> [En savoir plus sur les déclencheurs et les liaisons Azure Functions](functions-triggers-bindings.md)
