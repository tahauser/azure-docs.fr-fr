---
title: "Utiliser des déclencheurs et des liaisons dans Azure Functions"
description: "Découvrez comment utiliser des déclencheurs et des liaisons dans Azure Functions pour connecter l’exécution de votre code aux événements en ligne et aux services cloud."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: "azure functions, fonctions, traitement des événements, webhooks, calcul dynamique, architecture serverless"
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/21/2017
ms.author: glenga
ms.openlocfilehash: e3413c9e1055ca9198dae4a467bcf47372ad4ecb
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/30/2017
---
# <a name="azure-functions-triggers-and-bindings-concepts"></a>Concepts des déclencheurs et liaisons Azure Functions
Azure Functions vous permet d’écrire du code en réponse aux événements dans Azure et d’autres services, via des *déclencheurs* et *liaisons*. Cet article est une vue d’ensemble conceptuelle des déclencheurs et pour tous les langages de programmation pris en charge. Les fonctionnalités communes à toutes les liaisons sont décrites ici.

## <a name="overview"></a>Vue d'ensemble

Les déclencheurs et les liaisons permettent de définir de manière déclarative la façon dont une fonction est appelée et les données avec lesquelles elle fonctionne. Un *déclencheur* définit la façon dont une fonction est appelée. Une fonction ne doit avoir qu’un seul déclencheur. Les déclencheurs sont associés à des données, généralement la charge utile qui a déclenché la fonction.

Les *liaisons* d’entrée et de sortie fournissent un moyen déclaratif de se connecter à des données à partir de votre code. Comme pour les déclencheurs, vous spécifiez des chaînes de connexion et d’autres propriétés dans votre configuration des fonctions. Les liaisons sont facultatives et une fonction peut avoir plusieurs liaisons d’entrée et de sortie. 

À l’aide de déclencheurs et de liaisons, vous pouvez écrire du code qui est plus générique et qui ne code pas en dur les détails des services avec lesquels il interagit. Les données provenant deq services deviennent simplement des valeurs d’entrée pour votre code de fonction. Pour exporter des données vers un autre service (comme la création d’une nouvelle ligne dans le stockage de table Azure), utilisez la valeur de retour de la méthode. Autrement, si vous avez besoin de plusieurs valeurs de sortie, utilisez un objet d’assistance. Les déclencheurs et les liaisons ont une propriété **Nom**, qui est un identificateur que vous utilisez dans votre code pour accéder à la liaison.

Vous pouvez configurer des déclencheurs et liaisons dans l’onglet **Intégrer** du portail Azure Functions. En coulisses, l’interface utilisateur modifie un fichier appelé *function.json* dans le répertoire de la fonction. Vous pouvez modifier ce fichier en choisissant l’**Éditeur avancé**.

## <a name="supported-bindings"></a>Liaisons prises en charge

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

Pour plus d’informations sur les liaisons en préversion ou approuvées pour la production, consultez [Langages pris en charge](supported-languages.md).

## <a name="example-queue-trigger-and-table-output-binding"></a>Exemple : déclencheur de file d’attente et liaison de sortie de table

Imaginons que vous souhaitiez écrire une nouvelle ligne dans Stockage Table Azure à chaque fois qu’un nouveau message s’affiche dans Stockage File d’attente Azure. Ce scénario peut être implémenté à l’aide d’un déclencheur de file d’attente Azure et d’une liaison de sortie Stockage Table. 

Un déclencheur de stockage de file d’attente Azure nécessite les informations suivantes dans l’onglet **Intégrer** :

* Le nom du paramètre d’application qui contient la chaîne de connexion de compte Stockage Azure pour le stockage de file d’attente Azure
* Le nom de la file d’attente
* L’identificateur dans votre code pour lire le contenu du message de la file d’attente, tel que `order`.

Pour écrire dans Stockage Table Azure, utilisez une liaison de sortie avec les informations suivantes :

* Le nom du paramètre d’application qui contient la chaîne de connexion de compte Stockage Azure pour le Stockage Table Azure
* Le nom de la table
* L’identificateur dans votre code pour créer des éléments de sortie ou la valeur de retour de la fonction.

Les liaisons utilisent des valeurs de chaînes de connexion de valeurs stockées dans les paramètres d’application pour appliquer la bonne pratique selon laquelle *function.json* ne contient pas de secrets de service, mais seulement les noms des paramètres d’application.

Utilisez ensuite les identificateurs que vous avez fournis pour intégrer Stockage Azure dans votre code.

```cs
#r "Newtonsoft.Json"

using Newtonsoft.Json.Linq;

// From an incoming queue message that is a JSON object, add fields and write to Table Storage
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

Voici le *function.json* qui correspond au code précédent. Veuillez noter que la même configuration peut être utilisée, quelle que soit la langue de l’implémentation de la fonction.

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
Pour afficher et modifier le contenu de *function.json* dans le portail Azure, cliquez sur l’option **Éditeur avancé** dans l’onglet **Intégrer** de votre fonction.

Pour plus d’exemples de code et de détails sur l’intégration avec Stockage Azure, consultez [Déclencheurs et liaisons Azure Functions pour Stockage Azure](functions-bindings-storage.md).

## <a name="binding-direction"></a>Sens de la liaison

Tous les déclencheurs et liaisons ont une propriété `direction` dans le fichier *function.json* :

- Pour les déclencheurs, le sens est toujours `in`
- Les liaisons d’entrée et de sortie utilisent `in` et `out`
- Certaines liaisons prennent en charge un sens spécial `inout`. Si vous utilisez `inout`, seule l’option **Éditeur avancé** est disponible dans l’onglet **Intégrer**.

## <a name="using-the-function-return-type-to-return-a-single-output"></a>Utilisation du type de retour de fonction pour retourner une seule sortie

L’exemple précédent montre comment utiliser la valeur de retour de fonction pour fournir la sortie à une liaison, ce qui nécessite d’utiliser le paramètre de nom spécial `$return`. (Cela n’est pris en charge que dans les langages qui ont une valeur de retour, tels que C#, JavaScript et F#.) Si une fonction comporte plusieurs liaisons de sortie, utilisez `$return` pour une seule des liaisons de sortie. 

```json
// excerpt of function.json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

Les exemples ci-dessous montrent comment les types de retour sont utilisés avec des liaisons de sortie dans C#, JavaScript et F#.

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

Dans .NET, utilisez les types pour définir le type des données d’entrée. Par exemple, utilisez `string` pour établir une liaison au texte d’un déclencheur de file d’attente, un tableau d’octets à lire au format binaire et un type personnalisé pour désérialiser dans un objet OCT.

Pour les langages dont le type est dynamique, tels que JavaScript, vous devez utiliser la propriété `dataType` dans la définition de la liaison. Par exemple, pour lire le contenu d’une requête HTTP dans un format binaire, utilisez le type `binary` :

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

## <a name="trigger-metadata-properties"></a>Propriétés de métadonnées de déclencheur

En plus de la charge utile de données fournie par un déclencheur (par exemple, le message de file d’attente qui a déclenché une fonction), plusieurs déclencheurs fournissent des valeurs de métadonnées supplémentaires. Ces valeurs peuvent être utilisées comme paramètres d’entrée dans C# et F# ou comme propriétés sur l’objet `context.bindings` dans JavaScript. 

Par exemple, un déclencheur de file d’attente Stockage Azure prend en charge les propriétés suivantes :

* QueueTrigger - déclenchant le contenu du message si une chaîne valide
* DequeueCount
* ExpirationTime
* ID
* InsertionTime
* NextVisibleTime
* PopReceipt

Les détails des propriétés de métadonnées pour chaque déclencheur sont décrits dans la rubrique de référence correspondante. La documentation est également disponible dans l’onglet **Intégrer** du portail, dans la section **Documentation** située sous la zone de configuration de liaison.  

Par exemple, étant donné que les déclencheurs d’objet blob agissent avec un peu de retard, vous pouvez utiliser un déclencheur de file d’attente pour exécuter votre fonction (voir [Déclencheur Stockage Blob](functions-bindings-storage-blob.md#trigger)). Le message de file d’attente contiendra le nom de fichier du blob à déclencher. À l’aide de la propriété de métadonnées `queueTrigger`, vous pouvez spécifier ce comportement partout dans votre configuration, plutôt que dans votre code.

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

Les propriétés de métadonnées provenant d’un déclencheur peuvent également être utilisées dans une *expression de liaison* pour une autre liaison, comme décrit dans la section suivante.

## <a name="binding-expressions-and-patterns"></a>Modèles et expressions de liaison

Les *expressions de liaison* sont l’une des fonctionnalités les plus puissantes des déclencheurs et liaisons. Au sein de votre liaison, vous pouvez définir des modèles d’expression qui peuvent ensuite être utilisés dans d’autres liaisons ou votre code. Des métadonnées de déclencheur peuvent également être utilisées dans les expressions de liaison, comme indiqué dans l’exemple présenté dans la section précédente.

Par exemple, imaginons que vous vouliez redimensionner des images d’un conteneur Stockage Blob donné, et ce de manière similaire au modèle **Redimensionnement d’image** de la page **Nouvelle fonction**. Accédez à **Nouvelle fonction** -> Langage **C#** -> Scénario **Exemples** -> **ImageResizer-CSharp**. 

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

Veuillez noter que le paramètre `filename` est utilisé aussi bien dans la définition du déclencheur d’objet blob que dans la liaison de sortie d’objet blob. Ce paramètre peut également être utilisé dans le code de fonction.

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


### <a name="random-guids"></a>GUID aléatoires
Azure Functions fournit une syntaxe pratique pour générer des GUID dans vos liaisons, via l’expression de liaison `{rand-guid}`. L’exemple suivant l’utilise pour générer un nom d’objet blob unique : 

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{rand-guid}"
}
```

### <a name="current-time"></a>Heure actuelle

Vous pouvez utiliser l’expression de liaison `DateTime`, est résolue en `DateTime.UtcNow`.

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{DateTime}"
}
```

## <a name="bind-to-custom-input-properties-in-a-binding-expression"></a>Lier aux propriétés d’entrée personnalisées dans une expression de liaison

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

En C# et dans d’autres langages .NET, vous pouvez utiliser un schéma de liaison impératif, par opposition aux liaisons déclaratives *dans function.json*. La liaison impérative est utile lorsque les paramètres de liaison doivent être calculés au moment du runtime plutôt que lors de la conception. Pour plus d’informations, voir [Liaison lors de l’exécution via des liaisons impératives](functions-reference-csharp.md#imperative-bindings) dans la référence du développeur C#.

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur une liaison spécifique, consultez les articles suivants :

- [HTTP et webhooks](functions-bindings-http-webhook.md)
- [Minuteur](functions-bindings-timer.md)
- [Stockage de files d’attente](functions-bindings-storage-queue.md)
- [Stockage d’objets blob](functions-bindings-storage-blob.md)
- [Stockage Table](functions-bindings-storage-table.md)
- [Hub d’événements](functions-bindings-event-hubs.md)
- [Service Bus](functions-bindings-service-bus.md)
- [Azure Cosmos DB](functions-bindings-documentdb.md)
- [Microsoft Graph](functions-bindings-microsoft-graph.md)
- [SendGrid](functions-bindings-sendgrid.md)
- [Twilio](functions-bindings-twilio.md)
- [Notification Hubs](functions-bindings-notification-hubs.md)
- [Mobile Apps](functions-bindings-mobile-apps.md)
- [Fichier externe](functions-bindings-external-file.md)
