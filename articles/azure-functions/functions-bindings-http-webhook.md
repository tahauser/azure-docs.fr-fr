---
title: Liaisons HTTP et webhook Azure Functions
description: Découvrez comment utiliser des déclencheurs et des liaisons HTTP et webhook dans Azure Functions.
services: functions
documentationcenter: na
author: mattchenderson
manager: cfowler
editor: ''
tags: ''
keywords: azure functions, fonctions, traitement des événements, webhooks, calcul dynamique, architecture sans serveur, HTTP, API, REST
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/21/2017
ms.author: mahender
ms.openlocfilehash: a46177183035a53128c5341a3ce4c63dbc3a7497
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="azure-functions-http-and-webhook-bindings"></a>Liaisons HTTP et webhook Azure Functions

Cet article explique comment utiliser des liaisons HTTP dans Azure Functions. Azure Functions prend en charge les déclencheurs HTTP et les liaisons de sortie.

Vous pouvez personnaliser un déclencheur HTTP pour répondre aux [webhooks](https://en.wikipedia.org/wiki/Webhook). Un déclencheur webhook accepte uniquement une charge utile JSON et valide l’objet JSON. Il existe des versions spéciales du déclencheur webhook qui en facilitent la gestion depuis certains fournisseurs, comme GitHub et Slack.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="packages"></a>Packages

Les liaisons HTTP sont fournies dans le package NuGet [Microsoft.Azure.WebJobs.Extensions.Http](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Http). Le code source du package se trouve dans le référentiel GitHub [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Http/).

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="trigger"></a>Déclencheur

Le déclencheur HTTP vous permet d’appeler une fonction avec une requête HTTP. Vous pouvez utiliser un déclencheur HTTP pour générer des API serverless et répondre aux webhooks. 

Par défaut, un déclencheur HTTP répond à la requête avec un code d’état HTTP 200 OK et un corps vide. Pour modifier la réponse, configurez une [liaison de sortie HTTP](#http-output-binding).

## <a name="trigger---example"></a>Déclencheur - exemple

Consultez l’exemple propre à un langage particulier :

* [C#](#trigger---c-example)
* [Script C# (.csx)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [JavaScript](#trigger---javascript-example)

### <a name="trigger---c-example"></a>Déclencheur - exemple C#

L’exemple suivant montre une [fonction C#](functions-dotnet-class-library.md) qui recherche un paramètre `name` dans la chaîne de requête ou dans le corps de la requête HTTP.

```cs
[FunctionName("HttpTriggerCSharp")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)]HttpRequestMessage req, 
    TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");

    // parse query parameter
    string name = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
        .Value;

    // Get request body
    dynamic data = await req.Content.ReadAsAsync<object>();

    // Set name to query string or body data
    name = name ?? data?.name;

    return name == null
        ? req.CreateResponse(HttpStatusCode.BadRequest, "Please pass a name on the query string or in the request body")
        : req.CreateResponse(HttpStatusCode.OK, "Hello " + name);
}
```

### <a name="trigger---c-script-example"></a>Déclencheur - exemple Script C#

L’exemple suivant montre une liaison de déclencheur dans un fichier *function.json* et une [fonction de script C#](functions-reference-csharp.md) qui utilise la liaison. Cette fonction recherche un paramètre `name` dans la chaîne de requête ou dans le corps de la requête HTTP.

Voici les données de liaison dans le fichier *function.json* :

```json
{
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
    "authLevel": "function"
},
```

La section [configuration](#trigger---configuration) décrit ces propriétés.

Voici le code de script C# qui lie à `HttpRequestMessage` :

```csharp
using System.Net;
using System.Threading.Tasks;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info($"C# HTTP trigger function processed a request. RequestUri={req.RequestUri}");

    // parse query parameter
    string name = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
        .Value;

    // Get request body
    dynamic data = await req.Content.ReadAsAsync<object>();

    // Set name to query string or body data
    name = name ?? data?.name;

    return name == null
        ? req.CreateResponse(HttpStatusCode.BadRequest, "Please pass a name on the query string or in the request body")
        : req.CreateResponse(HttpStatusCode.OK, "Hello " + name);
}
```

Vous pouvez lier à un objet personnalisé au lieu de `HttpRequestMessage`. Cet objet est créé à partir du corps de la requête et analysé en tant que JSON. De même, un type peut être passé à la liaison de sortie de réponse HTTP, et être retourné en tant que corps de la réponse, avec un code d’état 200.

```csharp
using System.Net;
using System.Threading.Tasks;

public static string Run(CustomObject req, TraceWriter log)
{
    return "Hello " + req?.name;
}

public class CustomObject {
     public String name {get; set;}
}
}
```

### <a name="trigger---f-example"></a>Déclencheur - exemple F#

L’exemple suivant montre une liaison de déclencheur dans un fichier *function.json* et une [fonction F#](functions-reference-fsharp.md) qui utilise la liaison. Cette fonction recherche un paramètre `name` dans la chaîne de requête ou dans le corps de la requête HTTP.

Voici les données de liaison dans le fichier *function.json* :

```json
{
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
    "authLevel": "function"
},
```

La section [configuration](#trigger---configuration) décrit ces propriétés.

Voici le code F# :

```fsharp
open System.Net
open System.Net.Http
open FSharp.Interop.Dynamic

let Run(req: HttpRequestMessage) =
    async {
        let q =
            req.GetQueryNameValuePairs()
                |> Seq.tryFind (fun kv -> kv.Key = "name")
        match q with
        | Some kv ->
            return req.CreateResponse(HttpStatusCode.OK, "Hello " + kv.Value)
        | None ->
            let! data = Async.AwaitTask(req.Content.ReadAsAsync<obj>())
            try
                return req.CreateResponse(HttpStatusCode.OK, "Hello " + data?name)
            with e ->
                return req.CreateErrorResponse(HttpStatusCode.BadRequest, "Please pass a name on the query string or in the request body")
    } |> Async.StartAsTask
```

Vous avez besoin d’un fichier `project.json` qui utilise NuGet pour référencer les assemblys `FSharp.Interop.Dynamic` et `Dynamitey`, comme indiqué dans l’exemple suivant :

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

### <a name="trigger---javascript-example"></a>Déclencheur - exemple JavaScript

L’exemple suivant montre une liaison de déclencheur dans un fichier *function.json* et une [fonction JavaScript](functions-reference-node.md) qui utilise la liaison. Cette fonction recherche un paramètre `name` dans la chaîne de requête ou dans le corps de la requête HTTP.

Voici les données de liaison dans le fichier *function.json* :

```json
{
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
    "authLevel": "function"
},
```

La section [configuration](#trigger---configuration) décrit ces propriétés.

Voici le code JavaScript :

```javascript
module.exports = function(context, req) {
    context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);

    if (req.query.name || (req.body && req.body.name)) {
        context.res = {
            // status: 200, /* Defaults to 200 */
            body: "Hello " + (req.query.name || req.body.name)
        };
    }
    else {
        context.res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
    context.done();
};
```
     
## <a name="trigger---webhook-example"></a>Déclencheur - exemple de webhook

Consultez l’exemple propre à un langage particulier :

* [C#](#webhook---c-example)
* [Script C# (.csx)](#webhook---c-script-example)
* [F#](#webhook---f-example)
* [JavaScript](#webhook---javascript-example)

### <a name="webhook---c-example"></a>Webhook - exemple C#

L’exemple suivant montre une [fonction C#](functions-dotnet-class-library.md) qui envoie un message HTTP 200 en réponse à une requête JSON générique.

```cs
[FunctionName("HttpTriggerCSharp")]
public static HttpResponseMessage Run([HttpTrigger(AuthorizationLevel.Anonymous, WebHookType = "genericJson")] HttpRequestMessage req)
{
    return req.CreateResponse(HttpStatusCode.OK);
}
```

### <a name="webhook---c-script-example"></a>Webhook - exemple de script C#

L’exemple suivant montre une liaison de déclencheur de webhook dans un fichier *function.json* et une [fonction de script C#](functions-reference-csharp.md) qui utilise la liaison. Cette fonction enregistre les commentaires relatifs aux problèmes GitHub.

Voici les données de liaison dans le fichier *function.json* :

```json
{
    "webHookType": "github",
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
},
```

La section [configuration](#trigger---configuration) décrit ces propriétés.

Voici le code Script C# :

```csharp
#r "Newtonsoft.Json"

using System;
using System.Net;
using System.Threading.Tasks;
using Newtonsoft.Json;

public static async Task<object> Run(HttpRequestMessage req, TraceWriter log)
{
    string jsonContent = await req.Content.ReadAsStringAsync();
    dynamic data = JsonConvert.DeserializeObject(jsonContent);

    log.Info($"WebHook was triggered! Comment: {data.comment.body}");

    return req.CreateResponse(HttpStatusCode.OK, new {
        body = $"New GitHub comment: {data.comment.body}"
    });
}
```

### <a name="webhook---f-example"></a>Webhook - exemple F#

L’exemple suivant montre une liaison de déclencheur de webhook dans un fichier *function.json* et une [fonction F#](functions-reference-fsharp.md) qui utilise la liaison. Cette fonction enregistre les commentaires relatifs aux problèmes GitHub.

Voici les données de liaison dans le fichier *function.json* :

```json
{
    "webHookType": "github",
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
},
```

La section [configuration](#trigger---configuration) décrit ces propriétés.

Voici le code F# :

```fsharp
open System.Net
open System.Net.Http
open FSharp.Interop.Dynamic
open Newtonsoft.Json

type Response = {
    body: string
}

let Run(req: HttpRequestMessage, log: TraceWriter) =
    async {
        let! content = req.Content.ReadAsStringAsync() |> Async.AwaitTask
        let data = content |> JsonConvert.DeserializeObject
        log.Info(sprintf "GitHub WebHook triggered! %s" data?comment?body)
        return req.CreateResponse(
            HttpStatusCode.OK,
            { body = sprintf "New GitHub comment: %s" data?comment?body })
    } |> Async.StartAsTask
```

### <a name="webhook---javascript-example"></a>Webhook - exemple JavaScript

L’exemple suivant montre une liaison de déclencheur de webhook dans un fichier *function.json* et une [fonction JavaScript](functions-reference-node.md) qui utilise la liaison. Cette fonction enregistre les commentaires relatifs aux problèmes GitHub.

Voici les données de liaison dans le fichier *function.json* :

```json
{
    "webHookType": "github",
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
},
```

La section [configuration](#trigger---configuration) décrit ces propriétés.

Voici le code JavaScript :

```javascript
module.exports = function (context, data) {
    context.log('GitHub WebHook triggered!', data.comment.body);
    context.res = { body: 'New GitHub comment: ' + data.comment.body };
    context.done();
};
```

## <a name="trigger---attributes"></a>Déclencheur - attributs

Dans les [bibliothèques de classes C#](functions-dotnet-class-library.md), utilisez l’attribut [HttpTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions.Http/HttpTriggerAttribute.cs).

Vous pouvez définir le niveau d’autorisation et des méthodes HTTP autorisées dans les paramètres de constructeur d’attribut. Il existe des propriétés pour le type et le d’itinéraire webhook. Pour plus d’informations sur ces paramètres, consultez [Déclencheur - configuration](#trigger---configuration). Voici un attribut `HttpTrigger` dans une signature de méthode :

```csharp
[FunctionName("HttpTriggerCSharp")]
public static HttpResponseMessage Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, WebHookType = "genericJson")] HttpRequestMessage req)
{
    ...
}
 ```

Pour obtenir un exemple complet, consultez [Déclencheur - exemple C#](#trigger---c-example).

## <a name="trigger---configuration"></a>Déclencheur - configuration

Le tableau suivant décrit les propriétés de configuration de liaison que vous définissez dans le fichier *function.json* et l’attribut `HttpTrigger`.


|Propriété function.json | Propriété d’attribut |Description|
|---------|---------|----------------------|
| **type** | n/a| Obligatoire : doit être défini sur `httpTrigger`. |
| **direction** | n/a| Obligatoire : doit être défini sur `in`. |
| **name** | n/a| Obligatoire : nom de variable utilisé dans le code de la fonction pour la requête ou le corps de la requête. |
| <a name="http-auth"></a>**authLevel** |  **AuthLevel** |Détermine, le cas échéant, les clés qui doivent être présentes dans la requête pour appeler la fonction. Le niveau d’autorisation peut être l’une des valeurs suivantes : <ul><li><code>anonymous</code>&mdash;Aucune clé API n’est obligatoire.</li><li><code>function</code>&mdash;Une clé API spécifique à la fonction est obligatoire. Il s’agit de la valeur par défaut si aucune valeur n’est fournie.</li><li><code>admin</code>&mdash;La clé principale est obligatoire.</li></ul> Pour plus d’informations, consultez la section sur les [clés d’autorisation](#authorization-keys). |
| **methods** |**Méthodes** | Tableau des méthodes HTTP auxquelles la fonction répond. À défaut de spécification, la fonction répond à toutes les méthodes HTTP. Consultez [Personnaliser le point de terminaison HTTP](#trigger---customize-the-http-endpoint). |
| **route** | **Itinéraire** | Définit le modèle de routage, en contrôlant les URL de requête auxquelles votre fonction répond. La valeur par défaut est `<functionname>`. Pour plus d’informations, consultez [Personnaliser le point de terminaison HTTP](#customize-the-http-endpoint). |
| **webHookType** | **WebHookType** |Configure le déclencheur HTTP pour qu’il agisse en tant que récepteur de [Webhook](https://en.wikipedia.org/wiki/Webhook) pour le fournisseur spécifié. Ne définissez pas la propriété `methods` si vous définissez cette propriété. Le type de webhook peut être l’une des valeurs suivantes :<ul><li><code>genericJson</code>&mdash;Point de terminaison webhook à usage général sans logique pour un fournisseur spécifique. Ce paramètre limite les requêtes à celles utilisant HTTP POST et le type de contenu `application/json`.</li><li><code>github</code>&mdash;La fonction répond aux [Webhooks GitHub](https://developer.github.com/webhooks/). N’utilisez pas la propriété _authLevel_ avec des webhooks GitHub. Pour plus d’informations, consultez la section sur les webhooks GitHub plus loin dans cet article.</li><li><code>slack</code>&mdash;La fonction répond aux [Webhooks Slack](https://api.slack.com/outgoing-webhooks). N’utilisez pas la propriété _authLevel_ avec des webhooks Slack. Pour plus d’informations, consultez la section sur les webhooks Slack plus loin dans cet article.</li></ul>|

## <a name="trigger---usage"></a>Déclencheur - utilisation

Pour les fonctions C# et F #, vous pouvez déclarer le type de votre entrée de déclenchement en tant que `HttpRequestMessage` ou type personnalisé. Si vous choisissez `HttpRequestMessage`, vous obtenez un accès complet à l’objet de la requête. Pour un type personnalisé, le runtime Functions tente d’analyser le corps de la requête JSON pour définir les propriétés de l’objet. 

Dans le cas des fonctions JavaScript, le runtime Functions fournit le corps de requête plutôt que l’objet de requête. Pour plus d’informations, consultez l’[exemple de déclencheur JavaScript](#trigger---javascript-example).

### <a name="github-webhooks"></a>Webhooks GitHub

Pour répondre aux webhooks GitHub, commencez par créer votre fonction avec un déclencheur HTTP, puis définissez la propriété **webHookType** sur `github`. Ensuite, copiez son URL et sa clé API dans la page **Ajouter un Webhook** de votre dépôt GitHub. 

![](./media/functions-bindings-http-webhook/github-add-webhook.png)

Pour voir un exemple, consultez [Créer une fonction déclenchée par un webhook GitHub](functions-create-github-webhook-triggered-function.md).

### <a name="slack-webhooks"></a>Webhooks Slack

La webhook de Slack génère un jeton à votre place au lieu de vous laisser le spécifier. Vous devez donc configurer une clé spécifique de la fonction avec le jeton reçu de Slack. Consultez [Clés d’autorisation](#authorization-keys).

### <a name="customize-the-http-endpoint"></a>Personnaliser le point de terminaison HTTP

Par défaut, lorsque vous créez une fonction pour un déclencheur HTTP ou un WebHook, la fonction est adressable avec un itinéraire de la forme :

    http://<yourapp>.azurewebsites.net/api/<funcname> 

Vous pouvez personnaliser cet itinéraire en utilisant la propriété facultative `route` sur la liaison d’entrée du déclencheur HTTP. Par exemple, le fichier *function.json* suivant définit une propriété `route` pour un déclencheur HTTP :

```json
{
    "bindings": [
    {
        "type": "httpTrigger",
        "name": "req",
        "direction": "in",
        "methods": [ "get" ],
        "route": "products/{category:alpha}/{id:int?}"
    },
    {
        "type": "http",
        "name": "res",
        "direction": "out"
    }
    ]
}
```

Avec cette configuration, la fonction est désormais adressable avec l’itinéraire suivant au lieu de l’itinéraire d’origine.

```
http://<yourapp>.azurewebsites.net/api/products/electronics/357
```

Cela permet au code de la fonction de prendre en charge deux paramètres dans l’adresse, _category_ et _id_. Vous pouvez utiliser les [contraintes d’itinéraire des API Web](https://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2#constraints) de votre choix avec vos paramètres. Le code de la fonction C# suivant utilise les deux paramètres.

```csharp
public static Task<HttpResponseMessage> Run(HttpRequestMessage req, string category, int? id, 
                                                TraceWriter log)
{
    if (id == null)
        return  req.CreateResponse(HttpStatusCode.OK, $"All {category} items were requested.");
    else
        return  req.CreateResponse(HttpStatusCode.OK, $"{category} item with id = {id} has been requested.");
}
```

Voici le code de la fonction Node.js qui utilise les mêmes paramètres d’itinéraire.

```javascript
module.exports = function (context, req) {

    var category = context.bindingData.category;
    var id = context.bindingData.id;

    if (!id) {
        context.res = {
            // status: 200, /* Defaults to 200 */
            body: "All " + category + " items were requested."
        };
    }
    else {
        context.res = {
            // status: 200, /* Defaults to 200 */
            body: category + " item with id = " + id + " was requested."
        };
    }

    context.done();
} 
```

Par défaut, tous les itinéraires de fonction sont préfixés par *api*. Vous pouvez également personnaliser ou supprimer le préfixe avec la propriété `http.routePrefix` dans votre fichier [host.json](functions-host-json.md). L’exemple suivant supprime le préfixe d’itinéraire *api* en sélectionnant une chaîne vide pour le préfixe dans le fichier *host.json*.

```json
{
    "http": {
    "routePrefix": ""
    }
}
```

### <a name="authorization-keys"></a>Clés d’autorisation

Les déclencheurs HTTP vous permettent d’utiliser des clés pour une sécurité accrue. Un déclencheur HTTP standard peut les utiliser comme une clé API, en exigeant que la clé soit présente dans la requête. Les webhooks peuvent utiliser des clés pour autoriser des demandes de plusieurs façons, selon ce que le fournisseur prend en charge.

> [!NOTE]
> Lors de l’exécution de fonctions en local, l’autorisation est désactivée, peu importe l’ensemble `authLevel` dans `function.json`. Dès que vous publiez dans Azure Functions, `authLevel` prend effet immédiatement.

Les clés sont stockées dans votre Function App dans Azure, et chiffrées au repos. Pour afficher vos clés, créez-en de nouvelles, ou restaurez des clés avec de nouvelles valeurs, accédez à l’une de vos fonctions au sein du portail, puis sélectionnez « Gérer ». 

Il existe deux types de clés :

- **Clés d’hôte** : ces clés sont partagées par toutes les fonctions au sein de la Function App. Utilisées en tant que clés API, elles permettent d’accéder à toute fonction au sein de la Function App.
- **Clés de fonction** : ces clés s’appliquent uniquement aux fonctions spécifiques sous lesquelles elles sont définies. Utilisées en tant que clés API, elles permettent d’accéder uniquement à ces fonctions.

Chaque clé est nommée pour référence et il existe une clé par défaut (nommée « default ») au niveau fonction et hôte. Les clés de fonction prennent le pas sur les clés d’hôte. Quand deux clés portent le même nom, la clé de fonction est toujours utilisée.

La **clé principale** est une clé d’hôte par défaut nommée « _master » qui est définie pour chaque application de fonction. Cette clé ne peut pas être révoquée. Elle fournit un accès administratif aux API de runtime. L’utilisation de `"authLevel": "admin"` dans le JSON de liaison nécessite que cette clé soit présentée à la requête. Une autre clé entraînerait un échec de l’autorisation.

> [!IMPORTANT]  
> En raison des autorisations élevées qu’accorde la clé principale, vous ne devez pas partager celle-ci avec des tiers, ou la distribuer dans des applications clientes natives. Faites preuve de prudence lorsque vous choisissez le niveau d’autorisation administrateur.

### <a name="api-key-authorization"></a>Autorisation de clé API

Par défaut, un déclencheur HTTP nécessite qu’une clé API se trouve dans la requête HTTP. Votre requête HTTP doit donc ressembler à ceci :

    https://<yourapp>.azurewebsites.net/api/<function>?code=<ApiKey>

Cette clé peut être incluse dans une variable de chaîne de requête nommée `code` ou dans un en-tête HTTP `x-functions-key`. La valeur de la clé peut être toute clé de fonction définie pour la fonction, ou toute clé d’hôte.

Vous pouvez autoriser les requêtes anonymes, qui ne nécessitent pas de clés. Vous pouvez également exiger que la clé principale soit utilisée. Pour modifier le niveau d’autorisation par défaut, utilisez la propriété `authLevel` dans le JSON de liaison. Pour plus d’informations, consultez [Déclencheur - configuration](#trigger---configuration).

### <a name="keys-and-webhooks"></a>Clés et webhooks

Une autorisation de webhook est gérée par le composant récepteur de webhook, qui fait partie du déclencheur HTTP, et le mécanisme varie en fonction du type de webhook. Toutefois, chaque mécanisme dépend d’une clé. Par défaut, la clé de fonction nommée « default » est utilisée. Pour utiliser une autre clé, configurez le fournisseur de webhook pour envoyer le nom de clé avec la requête de l’une des manières suivantes :

- **Chaîne de requête** : le fournisseur passe le nom de clé dans le paramètre de chaîne de requête `clientid` (par exemple, `https://<yourapp>.azurewebsites.net/api/<funcname>?clientid=<keyname>`).
- **En-tête de demande** : le fournisseur transmet le nom de clé dans l’en-tête `x-functions-clientid`.

## <a name="trigger---limits"></a>Déclencheur : limites

La longueur de la requête HTTP est limitée à 100 Ko (102 400), tandis que la longueur de l’URL est limitée à 4 Ko (4 096). Ces limites sont spécifiées par l’élément `httpRuntime` du [fichier Web.config](https://github.com/Azure/azure-webjobs-sdk-script/blob/v1.x/src/WebJobs.Script.WebHost/Web.config) du runtime.

Si une fonction utilisant le déclencheur HTTP ne se termine pas en 2,5 minutes environ, la passerelle arrivera à expiration et retournera une erreur HTTP 502. La fonction continuera à s’exécuter, mais ne pourra pas renvoyer de réponse HTTP. Pour les fonctions à exécution longues, nous vous recommandons de suivre des modèles asynchrones et de retourner un emplacement où vous pouvez effectuer un test ping de l’état de la requête. Pour plus d’informations sur la durée d’exécution d’une fonction, consultez [Scale and hosting - Consumption plan](functions-scale.md#consumption-plan) (Mise à l’échelle et hébergement – Plan de consommation). 

## <a name="trigger---hostjson-properties"></a>Déclencheur - propriétés de host.json

Le fichier [host.json](functions-host-json.md) contient les paramètres qui contrôlent le comportement du déclencheur HTTP.

[!INCLUDE [functions-host-json-http](../../includes/functions-host-json-http.md)]

## <a name="output"></a>Sortie

Utilisez la liaison de sortie HTTP pour répondre à l’expéditeur de la demande HTTP. Cette liaison requiert un déclencheur HTTP, et vous permet de personnaliser la réponse associée à la demande du déclencheur. Si aucune liaison de sortie HTTP n’est spécifiée, un déclencheur HTTP retourne HTTP 200 OK avec un corps vide. 

## <a name="output---configuration"></a>Sortie - configuration

Pour les bibliothèques de classes C#, il n’existe aucune propriété de configuration de liaison propre à la sortie. Pour envoyer une réponse HTTP, choisissez un type de retour de fonction `HttpResponseMessage` ou `Task<HttpResponseMessage>`.

Pour les autres langages, une liaison de sortie HTTP est définie en tant qu’objet JSON dans le tableau `bindings` de function.json, comme dans l’exemple suivant :

```json
{
    "name": "res",
    "type": "http",
    "direction": "out"
}
```

Le tableau suivant décrit les propriétés de configuration de liaison que vous définissez dans le fichier *function.json*.

|Propriété  |Description  |
|---------|---------|
| **type** |Cette propriété doit être définie sur `http`. |
| **direction** | Cette propriété doit être définie sur `out`. |
|**name** | Nom de variable utilisé dans le code de fonction pour la réponse. |

## <a name="output---usage"></a>Sortie - utilisation

Vous pouvez utiliser le paramètre de sortie pour répondre à l’appelant HTTP ou Webhook. Vous pouvez également utiliser les modèles de réponse de la norme du langage. Par obtenir des exemples de réponse, consultez l’[exemple de déclencheur](#trigger---example) et l’[exemple de webhook](#trigger---webhook-example).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [En savoir plus sur les déclencheurs et les liaisons Azure Functions](functions-triggers-bindings.md)
