---
title: Liaisons de stockage Table Azure pour Azure Functions
description: Comprendre comment utiliser les liaisons de stockage de table Azure dans Azure Functions.
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: 
tags: 
keywords: "azure functions, fonctions, traitement des événements, calcul dynamique, architecture sans serveur"
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/08/2017
ms.author: tdykstra
ms.openlocfilehash: c132baad4d26fe481fa022329da32815b6994ad7
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="azure-table-storage-bindings-for-azure-functions"></a>Liaisons de stockage Table Azure pour Azure Functions

Cet article explique comment utiliser les liaisons de stockage de table Azure dans Azure Functions. Azure Functions prend en charge les liaisons d’entrée et de sortie pour Stockage de table Azure.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="input"></a>Entrée

La liaison d’entrée de stockage de table Azure permet de lire une table dans un compte de stockage Azure.

## <a name="input---example"></a>Entrée - exemple

Consultez l’exemple propre à un langage particulier :

* [C# - lire une entité](#input---c-example-1)
* [C# - lire plusieurs entités](#input---c-example-2)
* [Script C# - lire une entité](#input---c-script-example-1)
* [Script C# - lire plusieurs entités](#input---c-script-example-2)
* [F#](#input---f-example-2)
* [JavaScript](#input---javascript-example)

### <a name="input---c-example-1"></a>Entrée - exemple 1 C#

L’exemple suivant illustre une [fonction C#](functions-dotnet-class-library.md) qui lit une ligne de table unique. 

La valeur de clé de ligne "{queueTrigger}" indique que la clé de ligne provient de la chaîne de message de file d’attente.

```csharp
public class TableStorage
{
    public class MyPoco
    {
        public string PartitionKey { get; set; }
        public string RowKey { get; set; }
        public string Text { get; set; }
    }

    [FunctionName("TableInput")]
    public static void TableInput(
        [QueueTrigger("table-items")] string input, 
        [Table("MyTable", "MyPartition", "{queueTrigger}")] MyPoco poco, 
        TraceWriter log)
    {
        log.Info($"PK={poco.PartitionKey}, RK={poco.RowKey}, Text={poco.Text}");
    }
}
```

### <a name="input---c-example-2"></a>Entrée - exemple 2 C#

L’exemple suivant illustre une [fonction C#](functions-dotnet-class-library.md) qui lit plusieurs lignes de table. Notez que la classe `MyPoco` est dérivée de `TableEntity`.

```csharp
public class TableStorage
{
    public class MyPoco : TableEntity
    {
        public string Text { get; set; }
    }

    [FunctionName("TableInput")]
    public static void TableInput(
        [QueueTrigger("table-items")] string input, 
        [Table("MyTable", "MyPartition")] IQueryable<MyPoco> pocos, 
        TraceWriter log)
    {
        foreach (MyPoco poco in pocos)
        {
            log.Info($"PK={poco.PartitionKey}, RK={poco.RowKey}, Text={poco.Text}";
        }
    }
}
```

### <a name="input---c-script-example-1"></a>Entrée - exemple 1 Script C#

L’exemple suivant montre une liaison d’entrée de table dans un fichier *function.json* et un code [Script C#](functions-reference-csharp.md) qui utilise la liaison. La fonction utilise un déclencheur de file d’attente pour lire une seule ligne du tableau. 

Le fichier *function.json* spécifie une propriété `partitionKey` et une propriété `rowKey`. La valeur `rowKey` "{queueTrigger}" indique que la clé de ligne provient de la chaîne de message de file d’attente.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

La section [configuration](#input---configuration) décrit ces propriétés.

Voici le code Script C# :

```csharp
public static void Run(string myQueueItem, Person personEntity, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    log.Info($"Name in Person entity: {personEntity.Name}");
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}
```

### <a name="input---c-script-example-2"></a>Entrée - exemple 2 Script C#

L’exemple suivant montre une liaison d’entrée de table dans un fichier *function.json* et un code [Script C#](functions-reference-csharp.md) qui utilise la liaison. La fonction lit les entités d’une clé de partition qui est spécifiée dans un message de file d’attente.

Voici le fichier *function.json* :

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "tableBinding",
      "type": "table",
      "connection": "MyStorageConnectionAppSetting",
      "tableName": "Person",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

La section [configuration](#input---configuration) décrit ces propriétés.

Le code Script C# ajoute une référence au kit SDK Stockage Azure afin que le type d’entité puisse dériver de `TableEntity` :

```csharp
#r "Microsoft.WindowsAzure.Storage"
using Microsoft.WindowsAzure.Storage.Table;

public static void Run(string myQueueItem, IQueryable<Person> tableBinding, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    foreach (Person person in tableBinding.Where(p => p.PartitionKey == myQueueItem).ToList())
    {
        log.Info($"Name: {person.Name}");
    }
}

public class Person : TableEntity
{
    public string Name { get; set; }
}
```

### <a name="input---f-example"></a>Entrée - exemple F#

L’exemple suivant montre une liaison d’entrée de table dans un fichier *function.json* et du code [Script F#](functions-reference-fsharp.md) qui utilise la liaison. La fonction utilise un déclencheur de file d’attente pour lire une seule ligne du tableau. 

Le fichier *function.json* spécifie une propriété `partitionKey` et une propriété `rowKey`. La valeur `rowKey` "{queueTrigger}" indique que la clé de ligne provient de la chaîne de message de file d’attente.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

La section [configuration](#input---configuration) décrit ces propriétés.

Voici le code F# :

```fsharp
[<CLIMutable>]
type Person = {
  PartitionKey: string
  RowKey: string
  Name: string
}

let Run(myQueueItem: string, personEntity: Person) =
    log.Info(sprintf "F# Queue trigger function processed: %s" myQueueItem)
    log.Info(sprintf "Name in Person entity: %s" personEntity.Name)
```

### <a name="input---javascript-example"></a>Entrée - exemple JavaScript

L’exemple suivant montre une liaison d’entrée de table dans un fichier *function.json* et du [code JavaScript] (functions-reference-node.md) qui utilise la liaison. La fonction utilise un déclencheur de file d’attente pour lire une seule ligne du tableau. 

Le fichier *function.json* spécifie une propriété `partitionKey` et une propriété `rowKey`. La valeur `rowKey` "{queueTrigger}" indique que la clé de ligne provient de la chaîne de message de file d’attente.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

La section [configuration](#input---configuration) décrit ces propriétés.

Voici le code JavaScript :

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.log('Person entity name: ' + context.bindings.personEntity.Name);
    context.done();
};
```

## <a name="input---attributes"></a>Entrée - attributs
 
Dans les [bibliothèques de classes C#](functions-dotnet-class-library.md), utilisez les attributs suivants pour configurer un déclencheur d’entrée de table :

* [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TableAttribute.cs), défini dans le package NuGet [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs).

  Le constructeur de l’attribut prend le nom de la table, la clé de partition et la clé de ligne. Il peut être utilisé sur un paramètre out ou sur la valeur de retour de la fonction, comme indiqué dans l’exemple suivant :

  ```csharp
  [FunctionName("TableInput")]
  public static void Run(
      [QueueTrigger("table-items")] string input, 
      [Table("MyTable", "Http", "{queueTrigger}")] MyPoco poco, 
      TraceWriter log)
  {
      ...
  }
  ```

  Vous pouvez définir la propriété `Connection` pour spécifier le compte de stockage à utiliser, comme indiqué dans l’exemple suivant :

  ```csharp
  [FunctionName("TableInput")]
  public static void Run(
      [QueueTrigger("table-items")] string input, 
      [Table("MyTable", "Http", "{queueTrigger}", Connection = "StorageConnectionAppSetting")] MyPoco poco, 
      TraceWriter log)
  {
      ...
  }
  ```

  Pour obtenir un exemple complet, consultez [Entrée - exemple C#](#input---c-example).

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs), défini dans le package NuGet [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs)

  Fournit une autre manière de spécifier le compte de stockage à utiliser. Le constructeur prend le nom d’un paramètre d’application comportant une chaîne de connexion de stockage. L’attribut peut être appliqué au niveau du paramètre, de la méthode ou de la classe. L’exemple suivant montre le niveau de la classe et celui de la méthode :

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("TableInput")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  {
      ...
  }
  ```

Le compte de stockage à utiliser est déterminé dans l’ordre suivant :

* La propriété `Connection` de l’attribut `Table`.
* L’attribut `StorageAccount` appliqué au même paramètre que l’attribut `Table`.
* L’attribut `StorageAccount` appliqué à la fonction.
* L’attribut `StorageAccount` appliqué à la classe.
* Le compte de stockage par défaut pour l’application de fonction (paramètre d’application « AzureWebJobsStorage »).

## <a name="input---configuration"></a>Entrée - configuration

Le tableau suivant décrit les propriétés de configuration de liaison que vous définissez dans le fichier *function.json* et l’attribut `Table`.

|Propriété function.json | Propriété d’attribut |Description|
|---------|---------|----------------------|
|**type** | n/a | Cette propriété doit être définie sur `table`. Cette propriété est définie automatiquement lorsque vous créez la liaison dans le portail Azure.|
|**direction** | n/a | Cette propriété doit être définie sur `in`. Cette propriété est définie automatiquement lorsque vous créez la liaison dans le portail Azure. |
|**name** | n/a | Nom de la variable qui représente la table ou l’entité dans le code de la fonction. | 
|**tableName** | **TableName** | Nom de la table.| 
|**partitionKey** | **PartitionKey** |facultatif. Clé de partition de l’entité de table à lire. Consultez la section [utilisation](#input---usage) pour obtenir des conseils sur l’utilisation de cette propriété.| 
|**rowKey** |**RowKey** | facultatif. Clé de ligne de l’entité de table à lire. Consultez la section [utilisation](#input---usage) pour obtenir des conseils sur l’utilisation de cette propriété.| 
|**take** |**Take** | facultatif. Nombre maximal d’entités à lire en JavaScript. Consultez la section [utilisation](#input---usage) pour obtenir des conseils sur l’utilisation de cette propriété.| 
|**filter** |**Filter** | facultatif. Expression de filtre OData pour l’entrée de table dans JavaScript. Consultez la section [utilisation](#input---usage) pour obtenir des conseils sur l’utilisation de cette propriété.| 
|**Connexion** |**Connection** | Nom d’un paramètre d’application comportant la chaîne de connexion de stockage à utiliser pour cette liaison. Si le nom du paramètre d’application commence par « AzureWebJobs », vous ne pouvez spécifier que le reste du nom ici. Par exemple, si vous définissez `connection` sur « MyStorage », le runtime Functions recherche un paramètre d’application qui est nommé « AzureWebJobsMyStorage ». Si vous laissez `connection` vide, le runtime Functions utilise la chaîne de connexion de stockage par défaut dans le paramètre d’application nommé `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Entrée - utilisation

La liaison d’entrée de stockage de table prend en charge les scénarios suivants :

* **Lire une ligne dans C# ou Script C#**

  Définissez `partitionKey` et `rowKey`. Accédez aux données de la table à l’aide d’un paramètre de méthode `T <paramName>`. Dans Script C#, `paramName` est la valeur spécifiée dans la propriété `name` de *function.json*. `T` est généralement un type qui implémente `ITableEntity` ou est dérivé de `TableEntity`. Les propriétés `filter` et `take` ne sont pas utilisées dans ce scénario. 

* **Lire une ou plusieurs lignes en C# ou en Script C#**

  Accédez aux données de la table à l’aide d’un paramètre de méthode `IQueryable<T> <paramName>`. Dans Script C#, `paramName` est la valeur spécifiée dans la propriété `name` de *function.json*. `T` doit être généralement un type qui implémente `ITableEntity` ou est dérivé de `TableEntity`. Vous pouvez utiliser les méthodes `IQueryable` pour effectuer le filtrage voulu. Les propriétés `partitionKey`, `rowKey`, `filter` et `take` ne sont pas utilisées dans ce scénario.  

> [!NOTE]
> `IQueryable` ne fonctionne pas dans .NET Core, il ne fonctionne donc pas dans le [runtime v2 de Functions](functions-versions.md).

  Une alternative consiste à utiliser un paramètre de méthode `CloudTable paramName` pour lire la table en utilisant le kit SDK Stockage Azure.

* **Lire une ou plusieurs lignes en JavaScript**

  Définissez les propriétés `filter` et `take`. Ne définissez pas `partitionKey` ni `rowKey`. Accédez à l’entité (ou les entités) de table d’entrée à l’aide de `context.bindings.<name>`. Les objets désérialisés ont des propriétés `RowKey` et `PartitionKey`.

## <a name="output"></a>Sortie

Utilisez une liaison de sortie de stockage de table Azure pour écrire des entités dans une table d’un compte de Stockage Azure.

## <a name="output---example"></a>Sortie - exemple

Consultez l’exemple propre à un langage particulier :

* [C#](#output---c-example)
* [Script C# (.csx)](#output---c-script-example)
* [F#](#output---f-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>Sortie - exemple C#

L’exemple suivant illustre une [fonction C#](functions-dotnet-class-library.md) qui utilise un déclencheur HTTP pour écrire une ligne de table. 

```csharp
public class TableStorage
{
    public class MyPoco
    {
        public string PartitionKey { get; set; }
        public string RowKey { get; set; }
        public string Text { get; set; }
    }

    [FunctionName("TableOutput")]
    [return: Table("MyTable")]
    public static MyPoco TableOutput([HttpTrigger] dynamic input, TraceWriter log)
    {
        log.Info($"C# http trigger function processed: {input.Text}");
        return new MyPoco { PartitionKey = "Http", RowKey = Guid.NewGuid().ToString(), Text = input.Text };
    }
}
```

### <a name="output---c-script-example"></a>Sortie - exemple Script C#

L’exemple suivant montre une liaison de sortie de table dans un fichier *function.json* et un code [Script C#](functions-reference-csharp.md) qui utilise la liaison. La fonction écrit plusieurs entités de table.

Voici le fichier *function.json* :

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnectionAppSetting",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

La section [configuration](#output---configuration) décrit ces propriétés.

Voici le code Script C# :

```csharp
public static void Run(string input, ICollector<Person> tableBinding, TraceWriter log)
{
    for (int i = 1; i < 10; i++)
        {
            log.Info($"Adding Person entity {i}");
            tableBinding.Add(
                new Person() { 
                    PartitionKey = "Test", 
                    RowKey = i.ToString(), 
                    Name = "Name" + i.ToString() }
                );
        }

}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}

```

### <a name="output---f-example"></a>Sortie - exemple F#

L’exemple suivant montre une liaison de sortie de table dans un fichier *function.json* et un code [Script F#](functions-reference-fsharp.md) qui utilise la liaison. La fonction écrit plusieurs entités de table.

Voici le fichier *function.json* :

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnectionAppSetting",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

La section [configuration](#output---configuration) décrit ces propriétés.

Voici le code F# :

```fsharp
[<CLIMutable>]
type Person = {
  PartitionKey: string
  RowKey: string
  Name: string
}

let Run(input: string, tableBinding: ICollector<Person>, log: TraceWriter) =
    for i = 1 to 10 do
        log.Info(sprintf "Adding Person entity %d" i)
        tableBinding.Add(
            { PartitionKey = "Test"
              RowKey = i.ToString()
              Name = "Name" + i.ToString() })
```

### <a name="output---javascript-example"></a>Sortie - exemple JavaScript

L’exemple suivant montre une liaison de sortie de table dans un fichier *function.json* et une [fonction JavaScript](functions-reference-node.md) qui utilise la liaison. La fonction écrit plusieurs entités de table.

Voici le fichier *function.json* :

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnectionAppSetting",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

La section [configuration](#output---configuration) décrit ces propriétés.

Voici le code JavaScript :

```javascript
module.exports = function (context) {

    context.bindings.tableBinding = [];

    for (var i = 1; i < 10; i++) {
        context.bindings.tableBinding.push({
            PartitionKey: "Test",
            RowKey: i.toString(),
            Name: "Name " + i
        });
    }
    
    context.done();
};
```

## <a name="output---attributes"></a>Sortie - attributs

Dans les [bibliothèques de classes C#](functions-dotnet-class-library.md), utilisez l’attribut [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TableAttribute.cs) qui est défini dans le package NuGet [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs).

Le constructeur de l’attribut prend le nom de la table. Il peut être utilisé sur un paramètre `out` ou sur la valeur de retour de la fonction, comme indiqué dans l’exemple suivant :

```csharp
[FunctionName("TableOutput")]
[return: Table("MyTable")]
public static MyPoco TableOutput(
    [HttpTrigger] dynamic input, 
    TraceWriter log)
{
    ...
}
```

Vous pouvez définir la propriété `Connection` pour spécifier le compte de stockage à utiliser, comme indiqué dans l’exemple suivant :

```csharp
[FunctionName("TableOutput")]
[return: Table("MyTable", Connection = "StorageConnectionAppSetting")]
public static MyPoco TableOutput(
    [HttpTrigger] dynamic input, 
    TraceWriter log)
{
    ...
}
```

Pour obtenir un exemple complet, consultez [Sortie - exemple C#](#output---c-example).

Vous pouvez utiliser l’attribut `StorageAccount` pour spécifier le compte de stockage au niveau de la classe, de la méthode ou du paramètre. Pour plus d’informations, consultez [Entrée - attributs](#input---attributes).

## <a name="output---configuration"></a>Sortie - configuration

Le tableau suivant décrit les propriétés de configuration de liaison que vous définissez dans le fichier *function.json* et l’attribut `Table`.

|Propriété function.json | Propriété d’attribut |Description|
|---------|---------|----------------------|
|**type** | n/a | Cette propriété doit être définie sur `table`. Cette propriété est définie automatiquement lorsque vous créez la liaison dans le portail Azure.|
|**direction** | n/a | Cette propriété doit être définie sur `out`. Cette propriété est définie automatiquement lorsque vous créez la liaison dans le portail Azure. |
|**name** | n/a | Nom de variable utilisé dans le code de la fonction qui représente la table ou l’entité. La valeur doit être `$return` pour faire référence à la valeur de retour de la fonction.| 
|**tableName** |**TableName** | Nom de la table.| 
|**partitionKey** |**PartitionKey** | Clé de partition de l’entité de table à écrire. Consultez la section [utilisation](#output---usage) pour obtenir des conseils sur l’utilisation de cette propriété.| 
|**rowKey** |**RowKey** | Clé de ligne de l’entité de table à écrire. Consultez la section [utilisation](#output---usage) pour obtenir des conseils sur l’utilisation de cette propriété.| 
|**Connexion** |**Connection** | Nom d’un paramètre d’application comportant la chaîne de connexion de stockage à utiliser pour cette liaison. Si le nom du paramètre d’application commence par « AzureWebJobs », vous ne pouvez spécifier que le reste du nom ici. Par exemple, si vous définissez `connection` sur « MyStorage », le runtime Functions recherche un paramètre d’application qui est nommé « AzureWebJobsMyStorage ». Si vous laissez `connection` vide, le runtime Functions utilise la chaîne de connexion de stockage par défaut dans le paramètre d’application nommé `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Sortie - utilisation

La liaison de sortie de stockage de table prend en charge les scénarios suivants :

* **Écrire une ligne dans un langage**

  Dans C# et Script C#, accédez à l’entité de table de sortie en utilisant un paramètre de méthode comme `out T paramName` ou la valeur de retour de la fonction. Dans Script C#, `paramName` est la valeur spécifiée dans la propriété `name` de *function.json*. `T` peut être n’importe quel type sérialisable si la clé de partition et la clé de ligne sont fournies par le fichier *function.json* ou l’attribut `Table`. Sinon, `T` doit être un type qui inclut les propriétés `PartitionKey` et `RowKey`. Dans ce scénario, `T` implémente généralement `ITableEntity` ou est dérivé de `TableEntity`, mais ce n’est pas obligatoire.

* **Écrire une ou plusieurs lignes en C# ou en Script C#**

  Dans C# et Script C#, accédez à l’entité de table de sortie en utilisant un paramètre de méthode `ICollector<T> paramName` ou `ICollectorAsync<T> paramName`. Dans Script C#, `paramName` est la valeur spécifiée dans la propriété `name` de *function.json*. `T` spécifie le schéma des entités que vous souhaitez ajouter. En général, `T` est dérivé de `TableEntity` ou implémente `ITableEntity`, mais ce n’est pas obligatoire. Ni les valeurs de clé de partition et de clé de ligne dans *function.json*, ni le constructeur d’attribut `Table` ne sont utilisés dans ce scénario.

  Une autre solution consiste à utiliser un paramètre de méthode `CloudTable paramName` pour écrire la table en utilisant le kit SDK Stockage Azure.

* **Écrire une ou plusieurs lignes en JavaScript**

  Dans les fonctions JavaScript, accédez à la sortie de table avec `context.bindings.<name>`.

## <a name="exceptions-and-return-codes"></a>Exceptions et codes de retour

| Liaison | Informations de référence |
|---|---|
| Table | [Codes d’erreur de table](https://docs.microsoft.com/rest/api/storageservices/fileservices/table-service-error-codes) |
| Objet blob, Table, File d’attente | [Codes d’erreur de stockage](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Objet blob, Table, File d’attente | [Résolution des problèmes](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>étapes suivantes

> [!div class="nextstepaction"]
> [En savoir plus sur les déclencheurs et les liaisons Azure Functions](functions-triggers-bindings.md)
