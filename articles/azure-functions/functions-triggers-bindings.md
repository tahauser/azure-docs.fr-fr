---
title: "Déclencheurs et liaisons dans Azure Functions"
description: "Découvrez comment utiliser des déclencheurs et des liaisons dans Azure Functions pour connecter l’exécution de votre code aux événements en ligne et aux services cloud."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: "azure functions, fonctions, traitement des événements, webhooks, calcul dynamique, architecture sans serveur"
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/21/2017
ms.author: glenga
ms.openlocfilehash: a122271b5fdffd9db33a7dca5908e15f002041d7
ms.sourcegitcommit: 71fa59e97b01b65f25bcae318d834358fea5224a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/11/2018
---
# <a name="azure-functions-triggers-and-bindings-concepts"></a>Concepts des déclencheurs et liaisons Azure Functions

Cet article est une vue d’ensemble conceptuelle des déclencheurs et des liaisons dans Azure Functions. Les fonctionnalités communes à toutes les liaisons et toutes les langues prises en charge sont décrites ici.

## <a name="overview"></a>Vue d'ensemble

Un *déclencheur* définit la façon dont une fonction est appelée. Une fonction ne doit avoir qu’un seul déclencheur. Les déclencheurs sont associés à des données, généralement la charge utile qui a déclenché la fonction.

Les *liaisons* d’entrée et de sortie fournissent un moyen déclaratif de se connecter à des données à partir de votre code. Les liaisons sont facultatives et une fonction peut avoir plusieurs liaisons d’entrée et de sortie. 

Les déclencheurs et les liaisons vous permettent d’éviter de coder en dur les détails des services que vous utilisez. Votre fonction reçoit des données (par exemple, le contenu d’un message de la file d’attente) dans les paramètres de fonction. Vous envoyez des données (par exemple, pour créer un message de la file d’attente) à l’aide de la valeur de retour de la fonction, un paramètre `out` ou un [objet collecteur](functions-reference-csharp.md#writing-multiple-output-values).

Lorsque vous développez des fonctions en utilisant le portail Azure, les déclencheurs et les liaisons sont configurés dans un fichier *function.json*. Le portail fournit une interface utilisateur pour cette configuration, mais vous pouvez modifier le fichier directement en passant dans **l’Éditeur avancé**.

Lorsque vous développez des fonctions à l’aide de Visual Studio pour créer une bibliothèque de classes, vous configurez les déclencheurs et les liaisons en affectant des attributs aux méthodes et paramètres.

## <a name="supported-bindings"></a>Liaisons prises en charge

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

Pour plus d’informations sur les liaisons en préversion ou approuvées pour la production, consultez [Langages pris en charge](supported-languages.md).

## <a name="example-queue-trigger-and-table-output-binding"></a>Exemple : déclencheur de file d’attente et liaison de sortie de table

Imaginons que vous souhaitiez écrire une nouvelle ligne dans Stockage Table Azure à chaque fois qu’un nouveau message s’affiche dans Stockage File d’attente Azure. Ce scénario peut être implémenté à l’aide d’un déclencheur Stockage de file d’attente Azure et d’une liaison de sortie Stockage Table. 

Voici un fichier *function.json* pour ce scénario. 

```json
{
  "bindings": [
    {
      "name": "order",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "myqueue-items",
      "connection": "MY_STORAGE_ACCT_APP_SETTING"
    },
    {
      "name": "$return",
      "type": "table",
      "direction": "out",
      "tableName": "outTable",
      "connection": "MY_TABLE_STORAGE_ACCT_APP_SETTING"
    }
  ]
}
```

Le premier élément du tableau `bindings` est le déclencheur Stockage de file d’attente. Les propriétés `type` et `direction` identifient le déclencheur. La propriété `name` identifie le paramètre de fonction qui reçoit le contenu du message de la file d’attente. Le nom de la file d’attente à surveiller se trouve dans `queueName`, et la chaîne de connexion dans le paramètre d’application identifié par `connection`.

Le deuxième élément du tableau `bindings` est la liaison de sortie de Stockage Table Azure. Les propriétés `type` et `direction` identifient la liaison. La propriété `name` spécifie de quelle façon la fonction fournira la nouvelle ligne de table, dans ce cas à l’aide de la valeur de retour de fonction. Le nom de la table se trouve dans `tableName`, et la chaîne de connexion dans le paramètre d’application identifié par `connection`.

Pour afficher et modifier le contenu de *function.json* dans le portail Azure, cliquez sur l’option **Éditeur avancé** dans l’onglet **Intégrer** de votre fonction.

> [!NOTE]
> La valeur de `connection` est le nom d’un paramètre d’application qui contient la chaîne de connexion, et non la chaîne de connexion elle-même. Les liaisons utilisent des chaînes de connexion stockées dans les paramètres d’application pour appliquer la meilleure pratique qui consiste à ce que *function.json* ne contienne aucun secret de service.

Voici le code de script C# qui fonctionne avec ce déclencheur et la liaison. Notez que le nom du paramètre qui fournit le contenu de message de file d’attente est `order` ; ce nom est obligatoire, car la valeur de propriété `name` dans *function.json* est `order`. 

```cs
#r "Newtonsoft.Json"

using Newtonsoft.Json.Linq;

// From an incoming queue message that is a JSON object, add fields and write to Table storage
// The method return value creates a new row in Table Storage
public static Person Run(JObject order, TraceWriter log)
{
    return new Person() { 
            PartitionKey = "Orders", 
            RowKey = Guid.NewGuid().ToString(),  
            Name = order["Name"].ToString(),
            MobileNumber = order["MobileNumber"].ToString() };  
}
 
public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
    public string MobileNumber { get; set; }
}
```

Le même fichier function.json peut être utilisé avec une fonction JavaScript :

```javascript
// From an incoming queue message that is a JSON object, add fields and write to Table Storage
// The second parameter to context.done is used as the value for the new row
module.exports = function (context, order) {
    order.PartitionKey = "Orders";
    order.RowKey = generateRandomId(); 

    context.done(null, order);
};

function generateRandomId() {
    return Math.random().toString(36).substring(2, 15) +
        Math.random().toString(36).substring(2, 15);
}
```

Dans une bibliothèque de classes, les mêmes informations de déclencheur et de liaison &mdash; noms de file d’attente et de tables, comptes de stockage, paramètres de fonction pour l’entrée et la sortie &mdash; sont fournies par les attributs :

```csharp
 public static class QueueTriggerTableOutput
 {
     [FunctionName("QueueTriggerTableOutput")]
     [return: Table("outTable", Connection = "MY_TABLE_STORAGE_ACCT_APP_SETTING")]
     public static Person Run(
         [QueueTrigger("myqueue-items", Connection = "MY_STORAGE_ACCT_APP_SETTING")]JObject order, 
         TraceWriter log)
     {
         return new Person() {
                 PartitionKey = "Orders",
                 RowKey = Guid.NewGuid().ToString(),
                 Name = order["Name"].ToString(),
                 MobileNumber = order["MobileNumber"].ToString() };
     }
 }

 public class Person
 {
     public string PartitionKey { get; set; }
     public string RowKey { get; set; }
     public string Name { get; set; }
     public string MobileNumber { get; set; }
 }
```

## <a name="binding-direction"></a>Sens de la liaison

Tous les déclencheurs et liaisons ont une propriété `direction` dans le fichier *function.json* :

- Pour les déclencheurs, le sens est toujours `in`
- Les liaisons d’entrée et de sortie utilisent `in` et `out`
- Certaines liaisons prennent en charge un sens spécial `inout`. Si vous utilisez `inout`, seule l’option **Éditeur avancé** est disponible dans l’onglet **Intégrer**.

Lorsque vous utilisez des [attributs dans une bibliothèque de classes](functions-dotnet-class-library.md) pour configurer les déclencheurs et les liaisons, la direction est fournie dans un constructeur d’attribut ou déduite du type du paramètre.

## <a name="using-the-function-return-type-to-return-a-single-output"></a>Utilisation du type de retour de fonction pour retourner une seule sortie

L’exemple précédent montre comment utiliser la valeur de retour de la fonction pour fournir la sortie à une liaison, ce qui est spécifié dans *function.json* à l’aide de la valeur spéciale `$return` pour la propriété `name`. (Cela n’est pris en charge que dans les langages qui ont une valeur de retour, tels que les scripts C#, JavaScript et F#.) Si une fonction comporte plusieurs liaisons de sortie, utilisez `$return` pour une seule des liaisons de sortie. 

```json
// excerpt of function.json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

Les exemples ci-dessous montrent comment les types de retour sont utilisés avec des liaisons de sortie dans un script C#, JavaScript et F#.

```cs
// C# example: use method return value for output binding
public static string Run(WorkItem input, TraceWriter log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.Info($"C# script processed queue message. Item={json}");
    return json;
}
```

```cs
// C# example: async method, using return value for output binding
public static Task<string> Run(WorkItem input, TraceWriter log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.Info($"C# script processed queue message. Item={json}");
    return Task.FromResult(json);
}
```

```javascript
// JavaScript: return a value in the second parameter to context.done
module.exports = function (context, input) {
    var json = JSON.stringify(input);
    context.log('Node.js script processed queue message', json);
    context.done(null, json);
}
```

```fsharp
// F# example: use return value for output binding
let Run(input: WorkItem, log: TraceWriter) =
    let json = String.Format("{{ \"id\": \"{0}\" }}", input.Id)   
    log.Info(sprintf "F# script processed queue message '%s'" json)
    json
```

## <a name="binding-datatype-property"></a>Liaison de propriété Datatype

Dans .NET, utilisez le type de paramètre pour définir le type des données d’entrée. Par exemple, utilisez `string` pour établir une liaison au texte d’un déclencheur de file d’attente, un tableau d’octets à lire au format binaire et un type personnalisé pour désérialiser dans un objet OCT.

Pour les langages dont le type est dynamique, tels que JavaScript, utilisez la propriété `dataType` dans le fichier *function.json*. Par exemple, pour lire le contenu d’une requête HTTP au format binaire, définissez `dataType` sur `binary` :

```json
{
    "type": "httpTrigger",
    "name": "req",
    "direction": "in",
    "dataType": "binary"
}
```

Les autres options pour `dataType` sont `stream` et `string`.

## <a name="resolving-app-settings"></a>Résolution des paramètres de l’application

En tant que meilleure pratique, les chaînes de connexion et les secrets doivent être gérés avec des paramètres de l’application, plutôt qu’avec des fichiers de configuration. Cela limite l’accès à ces secrets et permet de stocker *function.json* en toute sécurité dans un référentiel de contrôle de code source public.

Les paramètres de l’application sont également utiles lorsque vous souhaitez modifier la configuration en fonction de l’environnement. Par exemple, dans un environnement de test, vous pourriez vouloir surveiller une autre file d’attente ou un autre conteneur Stockage Blob.

Les paramètres de l’application sont résolus à chaque fois qu’une valeur est placée entre des signes de pourcentage, comme `%MyAppSetting%`. Veuillez noter que la propriété `connection` des déclencheurs et liaisons est un cas spécial et résout automatiquement les valeurs en tant que paramètres de l’application. 

L’exemple suivant est un déclencheur de stockage de file d’attente Azure qui utilise un paramètre d’application `%input-queue-name%` pour définir la file d’attente sur laquelle effectuer le déclenchement.

```json
{
  "bindings": [
    {
      "name": "order",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "%input-queue-name%",
      "connection": "MY_STORAGE_ACCT_APP_SETTING"
    }
  ]
}
```

Vous pouvez utiliser la même approche dans les bibliothèques de classes :

```csharp
[FunctionName("QueueTrigger")]
public static void Run(
    [QueueTrigger("%input-queue-name%")]string myQueueItem, 
    TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
}
```

## <a name="trigger-metadata-properties"></a>Propriétés de métadonnées de déclencheur

En plus de la charge utile de données fournie par un déclencheur (par exemple, le message de file d’attente qui a déclenché une fonction), plusieurs déclencheurs fournissent des valeurs de métadonnées supplémentaires. Ces valeurs peuvent être utilisées comme paramètres d’entrée dans C# et F# ou comme propriétés sur l’objet `context.bindings` dans JavaScript. 

Par exemple, un déclencheur Stockage File d’attente Azure prend en charge les propriétés suivantes :

* QueueTrigger - déclenchant le contenu du message si une chaîne valide
* DequeueCount
* ExpirationTime
* ID
* InsertionTime
* NextVisibleTime
* PopReceipt

Ces valeurs de métadonnées sont accessibles dans les propriétés de fichier *function.json*. Par exemple, supposons que vous utilisez un déclencheur de file d’attente et que le message de la file d’attente contient le nom d’un objet blob que vous souhaitez lire. Dans le fichier *function.json*, vous pouvez utiliser la propriété de métadonnées `queueTrigger` dans la propriété de l’objet blob `path`, comme indiqué dans l’exemple suivant :

```json
  "bindings": [
    {
      "name": "myQueueItem",
      "type": "queueTrigger",
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "direction": "in",
      "connection": "MyStorageConnection"
    }
  ]
```

Les détails des propriétés de métadonnées pour chaque déclencheur sont décrits dans l’article de référence correspondante. Pour obtenir un exemple, consultez [Métadonnées de déclencheur de file d’attente](functions-bindings-storage-queue.md#trigger---message-metadata). La documentation est également disponible dans l’onglet **Intégrer** du portail, dans la section **Documentation** située sous la zone de configuration de liaison.  

## <a name="binding-expressions-and-patterns"></a>Modèles et expressions de liaison

Les *expressions de liaison* sont l’une des fonctionnalités les plus puissantes des déclencheurs et liaisons. Dans la configuration d’une liaison, vous pouvez définir des modèles d’expression qui peuvent ensuite être utilisés dans d’autres liaisons ou dans votre code. Des métadonnées de déclencheur peuvent également être utilisées dans les expressions de liaison, comme indiqué dans la section précédente.

Par exemple, imaginons que vous vouliez redimensionner des images d’un conteneur Stockage Blob donné, et ce, de manière similaire au modèle **Redimensionnement d’image** de la page **Nouvelle fonction** dans le portail Azure (voir le scénario **Exemples**). 

Voici la définition *function.json* :

```json
{
  "bindings": [
    {
      "name": "image",
      "type": "blobTrigger",
      "path": "sample-images/{filename}",
      "direction": "in",
      "connection": "MyStorageConnection"
    },
    {
      "name": "imageSmall",
      "type": "blob",
      "path": "sample-images-sm/{filename}",
      "direction": "out",
      "connection": "MyStorageConnection"
    }
  ],
}
```

Veuillez noter que le paramètre `filename` est utilisé dans la définition du déclencheur d’objet blob et dans la liaison de sortie d’objet blob. Ce paramètre peut également être utilisé dans le code de fonction.

```csharp
// C# example of binding to {filename}
public static void Run(Stream image, string filename, Stream imageSmall, TraceWriter log)  
{
    log.Info($"Blob trigger processing: {filename}");
    // ...
} 
```

<!--TODO: add JavaScript example -->
<!-- Blocked by bug https://github.com/Azure/Azure-Functions/issues/248 -->

La même possibilité d’utiliser des modèles et des expressions de liaison s’applique aux attributs dans les bibliothèques de classes. Par exemple, voici une fonction de redimensionnement d’image dans une bibliothèque de classes :

```csharp
[FunctionName("ResizeImage")]
[StorageAccount("AzureWebJobsStorage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image, 
    [Blob("sample-images-sm/{name}", FileAccess.Write)] Stream imageSmall, 
    [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageMedium)
{
    var imageBuilder = ImageResizer.ImageBuilder.Current;
    var size = imageDimensionsTable[ImageSize.Small];

    imageBuilder.Build(image, imageSmall,
        new ResizeSettings(size.Item1, size.Item2, FitMode.Max, null), false);

    image.Position = 0;
    size = imageDimensionsTable[ImageSize.Medium];

    imageBuilder.Build(image, imageMedium,
        new ResizeSettings(size.Item1, size.Item2, FitMode.Max, null), false);
}

public enum ImageSize { ExtraSmall, Small, Medium }

private static Dictionary<ImageSize, (int, int)> imageDimensionsTable = new Dictionary<ImageSize, (int, int)>() {
    { ImageSize.ExtraSmall, (320, 200) },
    { ImageSize.Small,      (640, 400) },
    { ImageSize.Medium,     (800, 600) }
};
```

### <a name="create-guids"></a>Créer des GUID

L’expression de liaison `{rand-guid}` crée un GUID. L’exemple suivant utilise un GUID pour créer un nom d’objet blob unique : 

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{rand-guid}"
}
```

### <a name="current-time"></a>Heure actuelle

L’expression de liaison `DateTime` se résout en `DateTime.UtcNow`.

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{DateTime}"
}
```

## <a name="bind-to-custom-input-properties"></a>Effectuer des liaisons à des propriétés d’entrée personnalisées

Les expressions de liaison peuvent également référencer des propriétés définies dans la charge utile du déclencheur. Par exemple, vous souhaiterez peut-être effectuer une liaison dynamique vers un fichier Stockage Blob à partir d’un nom de fichier fourni dans un webhook.

Par exemple, le *function.json* suivant utilise une propriété appelée `BlobName` provenant de la charge utile du déclencheur :

```json
{
  "bindings": [
    {
      "name": "info",
      "type": "httpTrigger",
      "direction": "in",
      "webHookType": "genericJson"
    },
    {
      "name": "blobContents",
      "type": "blob",
      "direction": "in",
      "path": "strings/{BlobName}",
      "connection": "AzureWebJobsStorage"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ]
}
```

Pour y parvenir dans C# et F #, vous devez définir un objet POCO qui définit les champs qui seront désérialisés dans la charge utile du déclencheur.

```csharp
using System.Net;

public class BlobInfo
{
    public string BlobName { get; set; }
}
  
public static HttpResponseMessage Run(HttpRequestMessage req, BlobInfo info, string blobContents)
{
    if (blobContents == null) {
        return req.CreateResponse(HttpStatusCode.NotFound);
    } 

    return req.CreateResponse(HttpStatusCode.OK, new {
        data = $"{blobContents}"
    });
}
```

Dans JavaScript, la désérialisation JSON est effectuée automatiquement et vous pouvez directement utiliser les propriétés.

```javascript
module.exports = function (context, info) {
    if ('BlobName' in info) {
        context.res = {
            body: { 'data': context.bindings.blobContents }
        }
    }
    else {
        context.res = {
            status: 404
        };
    }
    context.done();
}
```

## <a name="configuring-binding-data-at-runtime"></a>Configuration de la liaison des données lors de l’exécution

Avec C# et d’autres langages .NET, vous pouvez utiliser un schéma de liaison impératif, par opposition aux liaisons déclaratives dans *function.json*, et des attributs. La liaison impérative est utile lorsque les paramètres de liaison doivent être calculés au moment du runtime plutôt que lors de la conception. Pour plus d’informations, voir [Liaison lors de l’exécution via des liaisons impératives](functions-reference-csharp.md#imperative-bindings) dans la référence du développeur C#.

## <a name="functionjson-file-schema"></a>Schéma de fichier function.json

Le schéma de fichier *function.json* est disponible à l’adresse [http://json.schemastore.org/function](http://json.schemastore.org/function).

## <a name="next-steps"></a>étapes suivantes

Pour plus d’informations sur une liaison spécifique, consultez les articles suivants :

- [HTTP et webhooks](functions-bindings-http-webhook.md)
- [Minuteur](functions-bindings-timer.md)
- [Stockage de files d’attente](functions-bindings-storage-queue.md)
- [Stockage Blob](functions-bindings-storage-blob.md)
- [Stockage Table](functions-bindings-storage-table.md)
- [Concentrateur d’événements](functions-bindings-event-hubs.md)
- [Service Bus](functions-bindings-service-bus.md)
- [Azure Cosmos DB](functions-bindings-cosmosdb.md)
- [Microsoft Graph](functions-bindings-microsoft-graph.md)
- [SendGrid](functions-bindings-sendgrid.md)
- [Twilio](functions-bindings-twilio.md)
- [Notification Hubs](functions-bindings-notification-hubs.md)
- [Mobile Apps](functions-bindings-mobile-apps.md)
- [Fichier externe](functions-bindings-external-file.md)
