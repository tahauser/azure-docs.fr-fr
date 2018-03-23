---
title: Déclencheurs et liaisons dans Azure Functions
description: Découvrez comment utiliser des déclencheurs et des liaisons dans Azure Functions pour connecter l’exécution de votre code aux événements en ligne et aux services cloud.
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: ''
tags: ''
keywords: azure functions, fonctions, traitement des événements, webhooks, calcul dynamique, architecture sans serveur
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/07/2018
ms.author: glenga
ms.openlocfilehash: 559cfee1a8116703371a5641cf4534b7ad6f7578
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
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

## <a name="register-binding-extensions"></a>Inscrire des extensions de liaison

Dans la version 2.x du runtime Azure Functions, vous devez inscrire explicitement les [extensions de liaison](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/README.md) que vous utilisez dans votre application de fonction. 

Les extensions sont fournies sous la forme de packages NuGet, où le nom du package commence généralement par [microsoft.azure.webjobs.extensions](https://www.nuget.org/packages?q=microsoft.azure.webjobs.extensions).  La façon d’installer et d’inscrire des extensions de liaison dépend de la façon dont vous développez vos fonctions : 

+ [En local dans C# à l’aide de Visual Studio ou de VS Code](#precompiled-functions-c)
+ [En local avec Azure Functions Core Tools](#local-development-azure-functions-core-tools)
+ [Dans le portail Azure](#azure-portal-development) 

Dans la version 2.x, il existe un ensemble de liaisons qui ne sont pas fournies en tant qu’extensions. Vous n’avez pas besoin d’enregistrer d’extensions pour les déclencheurs et les liaisons suivants : HTTP, minuteur et stockage Azure. 

Pour plus d’informations sur la façon de configurer une application de fonction pour utiliser la version 2.x du runtime de Functions, consultez [Comment cibler des versions du runtime Azure Functions](set-runtime-version.md). La version 2.x du runtime Fonctions est actuellement en préversion. 

Les versions de package indiquées dans cette section sont fournies uniquement comme exemples. Consultez le [site NuGet.org](https://www.nuget.org/packages?q=microsoft.azure.webjobs.extensions) pour déterminer quelle version d’une extension donnée est requise par les autres dépendances dans votre application de fonction.    

###  <a name="local-c-development-using-visual-studio-or-vs-code"></a>Développement C# en local avec Visual Studio ou VS Code 

Lorsque vous utilisez Visual Studio ou Visual Studio Code pour développer localement des fonctions en C#, vous devez simplement ajouter le package NuGet pour l’extension. 

+ **Visual Studio** : utilisez les outils du gestionnaire de package NuGet. La commande [Install-Package](https://docs.microsoft.com/nuget/tools/ps-ref-install-package) suivante permet d’installer l’extension Microsoft Azure Cosmos DB à partir de la console du Gestionnaire de package :

    ```
    Install-Package Microsoft.Azure.WebJobs.Extensions.CosmosDB -Version 3.0.0-beta6 
    ```
+ **Visual Studio Code** : vous pouvez installer des packages à partir de l’invite de commandes à l’aide de la commande [dotnet add package](https://docs.microsoft.com/dotnet/core/tools/dotnet-add-package) dans l’interface CLI .NET, comme suit :

    ```
    dotnet add package Microsoft.Azure.WebJobs.Extensions.CosmosDB --version 3.0.0-beta6 
    ```

### <a name="local-development-azure-functions-core-tools"></a>Azure Functions Core Tools pour le développement local

[!INCLUDE [Full bindings table](../../includes/functions-core-tools-install-extension.md)]

### <a name="azure-portal-development"></a>Développement sur le portail Azure

Lorsque vous créez une fonction ou lorsque vous ajoutez une liaison à une fonction existante, vous êtes averti quand l’extension pour le déclencheur ou la liaison en cours d’ajout nécessite une inscription.   

Lorsqu’un avertissement s’affiche pour l’extension spécifique en cours d’installation, cliquez sur **Installer** pour inscrire l’extension. Vous n’avez besoin d’installer chaque extension qu’une seule fois pour une application de fonction donnée. 

>[!Note] 
>Le processus d’installation dans le portail peut prendre jusqu'à 10 minutes d’un plan de consommation.

## <a name="example-trigger-and-binding"></a>Exemple de liaison et déclencheur

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

Le deuxième élément du tableau `bindings` est la liaison de sortie de Stockage Table Azure. Les propriétés `type` et `direction` identifient la liaison. La propriété `name` spécifie de quelle façon la fonction fournit la nouvelle ligne de table, dans ce cas à l’aide de la valeur de retour de fonction. Le nom de la table se trouve dans `tableName`, et la chaîne de connexion dans le paramètre d’application identifié par `connection`.

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

Dans une bibliothèque de classes, les mêmes informations de déclencheur et de liaison &mdash; noms de file d’attente et de tables, comptes de stockage, paramètres de fonction pour l’entrée et la sortie &mdash; sont fournies par les attributs et non par un fichier .json. Voici un exemple :

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

## <a name="using-the-function-return-value"></a>Utilisation de la valeur de retour de la fonction

Dans les langages qui proposent une valeur de retour, vous pouvez lier une liaison de sortie à la valeur de retour :

* Dans une bibliothèque de classes C#, appliquez l’attribut de liaison de sortie à la valeur de retour de la méthode.
* Dans d’autres langages, définissez la propriété `name` dans *function.json* sur `$return`.

Si vous avez besoin d’écrire plusieurs éléments, utilisez un [objet collecteur](functions-reference-csharp.md#writing-multiple-output-values) au lieu de la valeur de retour. S’il existe plusieurs liaisons de sortie, utilisez la valeur de retour pour un seul d’entre eux.

Consultez l’exemple propre à un langage particulier :

* [C#](#c-example)
* [Script C# (.csx)](#c-script-example)
* [F#](#f-example)
* [JavaScript](#javascript-example)

### <a name="c-example"></a>Exemple en code C#

Voici du code C# qui utilise la valeur de retour pour une liaison de sortie, suivie d’un exemple asynchrone :

```cs
[FunctionName("QueueTrigger")]
[return: Blob("output-container/{id}")]
public static string Run([QueueTrigger("inputqueue")]WorkItem input, TraceWriter log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.Info($"C# script processed queue message. Item={json}");
    return json;
}
```

```cs
[FunctionName("QueueTrigger")]
[return: Blob("output-container/{id}")]
public static Task<string> Run([QueueTrigger("inputqueue")]WorkItem input, TraceWriter log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.Info($"C# script processed queue message. Item={json}");
    return Task.FromResult(json);
}
```

### <a name="c-script-example"></a>Exemple de script C#

Voici la liaison de sortie dans le fichier *function.json* :

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

Voici le code d’un script C#, suivi d’un exemple asynchrone :

```cs
public static string Run(WorkItem input, TraceWriter log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.Info($"C# script processed queue message. Item={json}");
    return json;
}
```

```cs
public static Task<string> Run(WorkItem input, TraceWriter log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.Info($"C# script processed queue message. Item={json}");
    return Task.FromResult(json);
}
```

### <a name="f-example"></a>Exemple F#

Voici la liaison de sortie dans le fichier *function.json* :

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

Voici le code F# :

```fsharp
let Run(input: WorkItem, log: TraceWriter) =
    let json = String.Format("{{ \"id\": \"{0}\" }}", input.Id)   
    log.Info(sprintf "F# script processed queue message '%s'" json)
    json
```

### <a name="javascript-example"></a>Exemple JavaScript

Voici la liaison de sortie dans le fichier *function.json* :

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

Dans JavaScript, la valeur de retour apparaît dans le second paramètre pour `context.done` :

```javascript
module.exports = function (context, input) {
    var json = JSON.stringify(input);
    context.log('Node.js script processed queue message', json);
    context.done(null, json);
}
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

## <a name="binding-expressions-and-patterns"></a>Modèles et expressions de liaison

Les *expressions de liaison* sont l’une des fonctionnalités les plus puissantes des déclencheurs et liaisons. Dans le fichier *function.json* ainsi que dans les paramètres et le code de la fonction, vous pouvez utiliser des expressions qui sont remplacées par des valeurs provenant de diverses sources.

La plupart des expressions sont identifiées par les accolades qui les entourent. Par exemple, dans une fonction de déclencheur de file d’attente, `{queueTrigger}` correspond au texte du message de file d’attente. Si la propriété `path` pour une liaison de sortie d’objet blob est `container/{queueTrigger}` et que la fonction est déclenchée par un message de file d’attente `HelloWorld`, alors un objet blob nommé `HelloWorld` est créé.

Types d’expressions de liaison

* [Paramètres de l’application](#binding-expressions---app-settings)
* [Nom du fichier de déclencheur](#binding-expressions---trigger-file-name)
* [Métadonnées de déclencheur](#binding-expressions---trigger-metadata)
* [Charges utiles JSON](#binding-expressions---json-payloads)
* [Nouveau GUID](#binding-expressions---create-guids)
* [Date et heure actuelles](#binding-expressions---current-time)

### <a name="binding-expressions---app-settings"></a>Expression de liaison - Paramètres d’application

En tant que meilleure pratique, les chaînes de connexion et les secrets doivent être gérés avec des paramètres de l’application, plutôt qu’avec des fichiers de configuration. Cela limite l’accès à ces secrets et permet de stocker des fichiers tels que *function.json* en toute sécurité dans des référentiels de contrôle de code source public.

Les paramètres de l’application sont également utiles lorsque vous souhaitez modifier la configuration en fonction de l’environnement. Par exemple, dans un environnement de test, vous pourriez vouloir surveiller une autre file d’attente ou un autre conteneur Stockage Blob.

Les expressions de liaison de paramètres d’application ne sont pas identifiées comme les autres expressions de liaison : elles sont entourées de signes de pourcentage et non d’accolades. Par exemple, si le chemin de liaison de sortie d’objet blob est `%Environment%/newblob.txt` et que la valeur du paramètre d’application `Environment` est `Development`, un objet blob sera créé dans le conteneur `Development`.

Lorsqu’une fonction s’exécute localement, les valeurs des paramètres de l’application proviennent du fichier *local.settings.json*.

Veuillez noter que la propriété `connection` des déclencheurs et liaisons est un cas spécial et résout automatiquement les valeurs en tant que paramètres de l’application, sans les signes de pourcentage. 

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

### <a name="binding-expressions---trigger-file-name"></a>Expressions de liaison - Nom du fichier de déclencheur

Le `path` pour un objet blob de déclencheur peut être un modèle qui vous permet de faire référence au nom de l’objet blob de déclenchement dans d’autres liaisons et codes de fonction. Le modèle peut également inclure des critères de filtre qui indiquent les objets blob qui peuvent déclencher un appel de fonction.

Par exemple, dans la liaison de déclencheur d’objet blob suivante, le modèle `path` est `sample-images/{filename}`, ce qui crée une expression de liaison nommée `filename` :

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
    ...
```

L’expression `filename` peut ensuite être utilisée dans une liaison de sortie pour spécifier le nom de l’objet blob en cours de création :

```json
    ...
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

Le code de fonction peut accéder à cette même valeur en utilisant `filename` comme nom de paramètre :

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

La même possibilité d’utiliser des modèles et des expressions de liaison s’applique aux attributs dans les bibliothèques de classes. Dans l’exemple suivant, les paramètres de constructeur d’attribut sont les mêmes valeurs `path` que les exemples de *function.json* précédents : 

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{filename}")] Stream image,
    [Blob("sample-images-sm/{filename}", FileAccess.Write)] Stream imageSmall,
    string filename,
    TraceWriter log)
{
    log.Info($"Blob trigger processing: {filename}");
    // ...
}

```

Vous pouvez également créer des expressions pour certaines parties du nom de fichier comme l’extension. Pour plus d’informations sur l’utilisation des expressions et des modèles dans la chaîne du chemin d’accès à l’objet blob, consultez l’article sur la [référence de liaison de stockage blob](functions-bindings-storage-blob.md).
 
### <a name="binding-expressions---trigger-metadata"></a>Expressions de liaison - Métadonnées de déclencheur

En plus de la charge utile de données fournie par un déclencheur (par exemple, le contenu du message de file d’attente qui a déclenché une fonction), plusieurs déclencheurs fournissent des valeurs de métadonnées supplémentaires. Ces valeurs peuvent être utilisées comme paramètres d’entrée dans C# et F# ou comme propriétés sur l’objet `context.bindings` dans JavaScript. 

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

### <a name="binding-expressions---json-payloads"></a>Expressions de liaison - Charges utiles JSON

Lorsque la charge utile d’un déclencheur est JSON, vous pouvez faire référence à ses propriétés dans la configuration pour d’autres liaisons dans la même fonction et dans le code de la fonction.

L’exemple suivant illustre le fichier *function.json* pour une fonction de Webhook qui reçoit un nom d’objet blob dans JSON : `{"BlobName":"HelloWorld.txt"}`. Une liaison d’entrée d’objet blob lit l’objet blob et la liaison de sortie HTTP retourne le contenu de l’objet blob dans la réponse HTTP. Notez que la liaison d’entrée d’objet blob obtient le nom de l’objet blob en faisant directement référence à la propriété `BlobName` (`"path": "strings/{BlobName}"`)

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
      "path": "strings/{BlobName.FileName}.{BlobName.Extension}",
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

Pour que cela fonctionne en C# et F#, vous avez besoin d’une classe qui définit les champs à désérialiser, comme dans l’exemple suivant :

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

Dans JavaScript, la désérialisation JSON est effectuée automatiquement.

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

#### <a name="dot-notation"></a>Notation par points

Si certaines des propriétés de votre charge utile JSON sont des objets dotés de propriétés, vous pouvez y faire référence directement à l’aide de la notation par points. Par exemple, supposons que votre JSON ressemble à l’exemple qui suit :

```json
{"BlobName": {
  "FileName":"HelloWorld",
  "Extension":"txt"
  }
}
```

Vous pouvez faire référence directement à `FileName` par l’expression `BlobName.FileName`. Avec ce format JSON, voici ce à quoi la propriété `path` de l’exemple précédent ressemblerait :

```json
"path": "strings/{BlobName.FileName}.{BlobName.Extension}",
```

En C#, vous avez besoin de deux classes :

```csharp
public class BlobInfo
{
    public BlobName BlobName { get; set; }
}
public class BlobName
{
    public string FileName { get; set; }
    public string Extension { get; set; }
}
```

### <a name="binding-expressions---create-guids"></a>Expressions de liaison - Création de GUIDS

L’expression de liaison `{rand-guid}` crée un GUID. Le chemin d’accès à l’objet blob suivant dans un fichier `function.json` crée un objet blob avec un nom similaire à ce qui suit : *50710cb5-84b9-4d87-9d83-a03d6976a682.txt*.

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{rand-guid}"
}
```

### <a name="binding-expressions---current-time"></a>Expressions de liaison - Heure actuelle

L’expression de liaison `DateTime` se résout en `DateTime.UtcNow`. Le chemin d’accès à l’objet blob suivant dans un fichier `function.json` crée un objet blob avec un nom similaire à ce qui suit : *2018-02-16T17-59-55Z.txt*.

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{DateTime}"
}
```

## <a name="binding-at-runtime"></a>Liaison au runtime

Avec C# et d’autres langages .NET, vous pouvez utiliser un schéma de liaison impératif, par opposition aux liaisons déclaratives dans *function.json*, et des attributs. La liaison impérative est utile lorsque les paramètres de liaison doivent être calculés au moment du runtime plutôt que lors de la conception. Pour plus d’informations, consultez les [informations de référence pour les développeurs C#](functions-dotnet-class-library.md#binding-at-runtime) ou les [informations de référence pour les développeurs de scripts C#](functions-reference-csharp.md#binding-at-runtime).

## <a name="functionjson-file-schema"></a>Schéma de fichier function.json

Le schéma de fichier *function.json* est disponible à l’adresse [http://json.schemastore.org/function](http://json.schemastore.org/function).

## <a name="handling-binding-errors"></a>Gestion des erreurs de liaison

[!INCLUDE [bindings errors intro](../../includes/functions-bindings-errors-intro.md)]

Pour obtenir des liens vers toutes les rubriques d’erreur pertinentes pour les divers services pris en charge par Azure Functions, voir la section [Codes d’erreur de liaison](functions-bindings-error-pages.md#binding-error-codes) de la rubrique de présentation [Gestion des erreurs dans Azure Functions](functions-bindings-error-pages.md).  

## <a name="next-steps"></a>Étapes suivantes

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
