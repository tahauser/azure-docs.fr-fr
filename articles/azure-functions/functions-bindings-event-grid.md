---
title: "Déclencheur Event Grid pour Azure Functions"
description: "Découvrez comment gérer les événements Event Grid dans Azure Functions."
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: 
tags: 
keywords: 
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/26/2018
ms.author: tdykstra
ms.openlocfilehash: e1d623c831a912598db72ccd0242cf827c88ee6c
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="event-grid-trigger-for-azure-functions"></a>Déclencheur Event Grid pour Azure Functions

Cet article explique comment gérer les événements [Event Grid](../event-grid/overview.md) dans Azure Functions.

Event Grid est un service Azure qui envoie des requêtes HTTP de notification au sujet des événements qui surviennent dans les *éditeurs*. L’éditeur d’un événement est le service ou la ressource qui est à l’origine de l’événement. Par exemple, un compte de Stockage Blob Azure est un éditeur, et un chargement d’objet blob un événement. [Certains services Azure intègrent la prise en charge de la publication d’événements sur Event Grid](../event-grid/overview.md#event-publishers). 

Les *gestionnaires* d’événements reçoivent et traitent les événements. Azure Functions est l’un des nombreux [services Azure qui intègrent la prise en charge de la gestion des événements Event Grid](../event-grid/overview.md#event-handlers). Cet article explique comment utiliser un déclencheur Event Grid pour appeler une fonction à la réception d’un événement en provenance d’Event Grid.

Si vous préférez, vous pouvez utiliser un déclencheur HTTP pour gérer les événements Event Grid ; consultez la section [Utiliser un déclencheur HTTP comme déclencheur Event Grid](#use-an-http-trigger-as-an-event-grid-trigger) dans la suite de cet article.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="example"></a>exemples

Consultez l’exemple de déclencheur Event Grid correspondant au langage souhaité :

* [C#](#c-example)
* [Script C# (.csx)](#c-script-example)
* [JavaScript](#javascript-example)

Vous trouverez un exemple de déclencheur HTTP dans la section [Guide pratique pour utiliser un déclencheur HTTP](#use-an-http-trigger-as-an-event-grid-trigger) dans la suite de cet article.

### <a name="c-example"></a>Exemple en code C#

L’exemple suivant montre une [fonction C#](functions-dotnet-class-library.md) qui consigne certains des champs communs à tous les événements et toutes les données propres aux événements.

```cs
[FunctionName("EventGridTest")]
public static void EventGridTest([EventGridTrigger] EventGridEvent eventGridEvent, TraceWriter log)
{
    log.Info("C# Event Grid function processed a request.");
    log.Info($"Subject: {eventGridEvent.Subject}");
    log.Info($"Time: {eventGridEvent.EventTime}");
    log.Info($"Data: {eventGridEvent.Data.ToString()}");
}
```

L’attribut `EventGridTrigger` est défini dans le package NuGet [Microsoft.Azure.WebJobs.Extensions.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid).

### <a name="c-script-example"></a>Exemple de script C#

L’exemple suivant montre une liaison de déclencheur dans un fichier *function.json* et une [fonction de script C#](functions-reference-csharp.md) qui utilise la liaison. La fonction consigne certains des champs communs à tous les événements et toutes les données propres aux événements.

Voici les données de liaison dans le fichier *function.json* :

```json
{
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "eventGridEvent",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

Voici le code Script C# :

```csharp
#r "Newtonsoft.Json"
#r "Microsoft.Azure.WebJobs.Extensions.EventGrid"
using Microsoft.Azure.WebJobs.Extensions.EventGrid;

public static void Run(EventGridEvent eventGridEvent, TraceWriter log)
{
    log.Info("C# Event Grid function processed a request.");
    log.Info($"Subject: {eventGridEvent.Subject}");
    log.Info($"Time: {eventGridEvent.EventTime}");
    log.Info($"Data: {eventGridEvent.Data.ToString()}");
}
```

### <a name="javascript-example"></a>Exemple JavaScript

L’exemple suivant montre une liaison de déclencheur dans un fichier *function.json* et une [fonction JavaScript](functions-reference-node.md) qui utilise la liaison. La fonction consigne certains des champs communs à tous les événements et toutes les données propres aux événements.

Voici les données de liaison dans le fichier *function.json* :

```json
{
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "eventGridEvent",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

Voici le code JavaScript :

```javascript
module.exports = function (context, eventGridEvent) {
    context.log("JavaScript Event Grid function processed a request.");
    context.log("Subject: " + eventGridEvent.subject);
    context.log("Time: " + eventGridEvent.eventTime);
    context.log("Data: " + JSON.stringify(eventGridEvent.data));
    context.done();
};
```
     
## <a name="attributes"></a>Attributs

Dans les [Bibliothèques de classes C#](functions-dotnet-class-library.md), utilisez l’attribut [EventGridTrigger](https://github.com/Azure/azure-functions-eventgrid-extension/blob/master/src/EventGridExtension/EventGridTriggerAttribute.cs) défini dans le package NuGet [Microsoft.Azure.WebJobs.Extensions.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid).

Voici un attribut `EventGridTrigger` dans une signature de méthode :

```csharp
[FunctionName("EventGridTest")]
public static void EventGridTest([EventGridTrigger] EventGridEvent eventGridEvent, TraceWriter log)
{
    ...
}
 ```

Vous trouverez un exemple complet sur la page [Exemple C#](#c-example).

## <a name="configuration"></a>Configuration

Le tableau suivant décrit les propriétés de configuration de liaison que vous définissez dans le fichier *function.json*. Il n’y a aucun paramètre de constructeur ni aucune propriété à définir dans l’attribut `EventGridTrigger`.

|Propriété function.json |DESCRIPTION|
|---------|---------|----------------------|
| **type** | Obligatoire : doit être défini sur `eventGridTrigger`. |
| **direction** | Obligatoire : doit être défini sur `in`. |
| **name** | Obligatoire : nom de variable utilisé dans le code de fonction pour le paramètre qui reçoit les données de l’événement. |

## <a name="usage"></a>Usage

Pour les fonctions C# et F#, déclarez votre entrée de déclencheur avec le type `EventGridEvent` ou un type personnalisé. Dans le cas d’un type personnalisé, le runtime Functions tente d’analyser le JSON de l’événement pour définir les propriétés de l’objet.

Pour les fonctions JavaScript, le paramètre nommé par la propriété *function.json* `name` comporte une référence à l’objet de l’événement.

## <a name="event-schema"></a>Schéma d’événement

Les données d’un événement Event Grid sont réceptionnées sous la forme d’un objet JSON dans le corps d’une requête HTTP, comme dans l’exemple suivant :

```json
[{
  "topic": "/subscriptions/{subscriptionid}/resourceGroups/eg0122/providers/Microsoft.Storage/storageAccounts/egblobstore",
  "subject": "/blobServices/default/containers/{containername}/blobs/blobname.jpg",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2018-01-23T17:02:19.6069787Z",
  "id": "{guid}",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "{guid}",
    "requestId": "{guid}",
    "eTag": "0x8D562831044DDD0",
    "contentType": "application/octet-stream",
    "contentLength": 2248,
    "blobType": "BlockBlob",
    "url": "https://egblobstore.blob.core.windows.net/{containername}/blobname.jpg",
    "sequencer": "000000000000272D000000000003D60F",
    "storageDiagnostics": {
      "batchId": "{guid}"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

Cet exemple est un tableau comportant un seul élément. Event Grid envoie toujours un tableau et peut y inclure plusieurs événements. Le runtime appelle la fonction une fois par élément du tableau.

Les propriétés de niveau supérieur présentes dans les données JSON de l’événement sont les mêmes quel que soit le type d’événement, tandis que la valeur de la propriété `data` est propre à chaque type d’événement. L’exemple correspond à un événement de Stockage Blob.

Vous trouverez des explications sur les propriétés communes et propres aux événements dans la section [Propriétés des événements](../event-grid/event-schema.md#event-properties) de la documentation Event Grid.

Le type `EventGridEvent` définit uniquement les propriétés de niveau supérieur ; la propriété `Data` est un `JObject`. 

## <a name="create-a-subscription"></a>Création d’un abonnement

Pour pouvoir recevoir des requêtes HTTP Event Grid, créez un abonnement Event Grid spécifiant l’URL de point de terminaison qui appelle la fonction.

### <a name="azure-portal"></a>Portail Azure

Pour les fonctions développées sur le Portail Azure avec le déclencheur Event Grid, sélectionnez **Ajouter un abonnement Event Grid**.

![Créer un abonnement sur le Portail](media/functions-bindings-event-grid/portal-sub-create.png)

Ce lien ouvre la page **Créer un abonnement aux événements** sur le Portail, en préremplissant l’URL de point de terminaison.

![URL de point de terminaison préremplie](media/functions-bindings-event-grid/endpoint-url.png)

Pour plus d’informations sur la création d’abonnements à l’aide du Portail Azure, consultez la section [Créer un événement personnalisé - Portail Azure](../event-grid/custom-event-quickstart-portal.md) dans la documentation Event Grid.

### <a name="azure-cli"></a>Azure CLI

Pour créer un abonnement avec [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest), utilisez la commande [az eventgrid event-subscription create](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az_eventgrid_event_subscription_create).

La commande a besoin de l’URL de point de terminaison qui appelle la fonction. L’exemple suivant illustre le modèle d’URL :

```
https://{functionappname}.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName={functionname}&code={systemkey}
```

La clé système est une clé d’autorisation qui doit être incluse dans l’URL de point de terminaison d’un déclencheur Event Grid. La section suivante explique comment l’obtenir.

Voici un exemple d’abonnement à un compte de Stockage Blob (avec un espace réservé pour la clé système) :

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup \
--provider-namespace Microsoft.Storage --resource-type storageAccounts \
--resource-name glengablobstorage --name myFuncSub  \
--included-event-types Microsoft.Storage.BlobCreated \
--subject-begins-with /blobServices/default/containers/images/blobs/ \
--endpoint https://glengastorageevents.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName=imageresizefunc&code=LUwlnhIsNtSiUjv/sNtSiUjvsNtSiUjvsNtSiUjvYb7XDonDUr/RUg==
```

Pour plus d’informations sur la création d’un abonnement, consultez la section [Guide de démarrage rapide sur le Stockage Blob](../storage/blobs/storage-blob-event-quickstart.md#subscribe-to-your-storage-account) ou les autres guides de démarrage rapide Event Grid.

### <a name="get-the-system-key"></a>Obtenir la clé système

Vous pouvez obtenir la clé système à l’aide de l’API suivante (HTTP GET) :

```
http://{functionappname}.azurewebsites.net/admin/host/systemkeys/eventgridextensionconfig_extension?code={adminkey}
```

Il s’agit d’une API d’administration, qui a donc besoin de votre [clé d’administration](functions-bindings-http-webhook.md#authorization-keys). Ne confondez pas la clé système (permettant d’appeler une fonction de déclenchement Event Grid) avec la clé d’administration (servant à effectuer des tâches d’administration sur l’application de fonction). Veillez à utiliser la clé système pour vous abonner à une rubrique Event Grid.

Voici un exemple de réponse fournissant la clé système :

```
{
  "name": "eventgridextensionconfig_extension",
  "value": "{the system key for the function}",
  "links": [
    {
      "rel": "self",
      "href": "{the URL for the function, without the system key}"
    }
  ]
}
```

Pour plus d’informations, consultez la section [Clés d’autorisation](functions-bindings-http-webhook.md#authorization-keys) dans l’article de référence sur les déclencheurs HTTP. 

Vous pouvez également envoyer une requête HTTP PUT pour spécifier la valeur de la clé vous-même.

## <a name="local-testing-with-requestbin"></a>Tests locaux avec RequestBin

Pour qu’un déclencheur Event Grid soit testé en local, les requêtes HTTP Event Grid doivent partir de leur origine dans le cloud et arriver sur l’ordinateur local. Vous pouvez pour cela capturer les requêtes en ligne et les renvoyer manuellement sur votre ordinateur local :

2. [Créez un point de terminaison RequestBin](#create-a-RequestBin-endpoint).
3. [Créez un abonnement Event Grid](#create-an-event-grid-subscription) qui envoie les événements au point de terminaison RequestBin.
4. [Générez une requête](#generate-a-request) et copiez le corps de la requête à partir du site RequestBin.
5. [Publiez (POST) manuellement la requête](#manually-post-the-request) sur l’URL localhost de votre fonction de déclenchement Event Grid.

À l’issue des tests, vous pourrez utiliser le même abonnement en production en mettant à jour le point de terminaison. Utilisez la commande Azure CLI [az eventgrid event-subscription update](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az_eventgrid_event_subscription_update).

### <a name="create-a-requestbin-endpoint"></a>Créer un point de terminaison RequestBin

RequestBin est un outil open source qui accepte les requêtes HTTP et montre le corps de la requête. L’URL http://requestb.in fait l’objet d’un traitement spécial de la part d’Azure Event Grid. Pour faciliter les tests, Event Grid envoie des événements à l’URL RequestBin sans avoir besoin d’une réponse correcte aux requêtes de validation d’abonnement. Deux autres outils de test reçoivent le même traitement : http://webhookinbox.com et http://hookbin.com.

RequestBin n’est pas destiné à une utilisation avec débit élevé. Si vous envoyez plusieurs événements par push en simultané, vous pouvez ne pas voir tous les événements dans l’outil.

Créez un point de terminaison.

![Créer un point de terminaison RequestBin](media/functions-bindings-event-grid/create-requestbin.png)

Copiez l’URL de point de terminaison.

![Copier le point de terminaison RequestBin](media/functions-bindings-event-grid/save-requestbin-url.png)

### <a name="create-an-event-grid-subscription"></a>Créer un abonnement Event Grid

Créez un abonnement Event Grid du type à tester, et donnez-lui votre point de terminaison RequestBin. Pour plus d’informations sur la création d’abonnements, consultez la section [Créer un abonnement](#create-a-subscription) au début de cet article.

### <a name="generate-a-request"></a>Générer une requête

Déclenchez un événement qui génèrera du trafic HTTP sur votre point de terminaison RequestBin.  Par exemple, si vous avez créé un abonnement au Stockage Blob, chargez ou supprimez un objet blob. Lorsqu’une requête s’affiche sur votre page RequestBin, copiez le corps de la requête.

La requête de validation d’abonnement sera reçue la première ; ignorez toutes les requêtes de validation et copiez la requête d’événement.

![Copier le corps de la requête à partir de RequestBin](media/functions-bindings-event-grid/copy-request-body.png)

### <a name="manually-post-the-request"></a>Publier (POST) manuellement la requête

Exécutez votre fonction Event Grid en local.

Utilisez un outil comme [Postman](https://www.getpostman.com/) ou [curl](https://curl.haxx.se/docs/httpscripting.html) pour créer une requête HTTP POST :

* Définissez un en-tête `Content-Type: application/json`.
* Définissez un en-tête `aeg-event-type: Notification`.
* Collez les données RequestBin dans le corps de la requête. 
* Publiez (POST) sur l’URL de votre fonction de déclenchement Event Grid, suivant ce modèle :

```
http://localhost:7071/admin/extensions/EventGridExtensionConfig?functionName={methodname}
``` 

Le paramètre `functionName` doit être le nom de la méthode, et non le nom spécifié dans l’attribut `FunctionName`. C’est pourquoi, lorsqu’un projet comporte plusieurs fonctions, celles-ci doivent avoir des noms uniques de méthode (il ne faut pas qu’elles soient toutes nommées `Run`) pour qu’il soit possible de tester les déclencheurs Event Grid en local.

Les captures d’écran suivantes montrent les en-têtes et le corps de la requête dans Postman :

![En-têtes dans Postman](media/functions-bindings-event-grid/postman2.png)

![Corps de la requête dans Postman](media/functions-bindings-event-grid/postman.png)

La fonction de déclenchement Event Grid s’exécute et affiche des journaux de ce type :

![Exemple de journaux d’une fonction de déclenchement Event Grid](media/functions-bindings-event-grid/eg-output.png)

## <a name="local-testing-with-ngrok"></a>Tests locaux avec ngrok

Une autre possibilité, pour tester un déclencheur Event Grid en local, consiste à automatiser la connexion HTTP entre Internet et l’ordinateur de développement. Vous pouvez pour cela utiliser un outil open source nommé [ngrok](https://ngrok.com/) :

3. [Créez un point de terminaison ngrok](#create-an-ngrok-endpoint).
4. [Exécutez la fonction de déclenchement Event Grid](#run-the-event-grid-trigger-function).
5. [Créez un abonnement Event Grid](#create-a-subscription) qui envoie les événements au point de terminaison ngrok.
6. [Déclenchez un événement](#trigger-an-event).

À l’issue des tests, vous pourrez utiliser le même abonnement en production en mettant à jour le point de terminaison. Utilisez la commande Azure CLI [az eventgrid event-subscription update](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az_eventgrid_event_subscription_update).

### <a name="create-an-ngrok-endpoint"></a>Créer un point de terminaison ngrok

Téléchargez *ngrok.exe* sur [ngrok](https://ngrok.com/), et exécutez-le avec la commande suivante :

```
ngrok http -host-header=localhost 7071
```

Le paramètre -host-header est nécessaire, car le runtime de la fonction attend des requêtes de localhost en cas d’exécution sur localhost. 7071 est le numéro de port par défaut pour les exécutions en local.

La commande crée une sortie de ce type :

```
Session Status                online
Version                       2.2.8
Region                        United States (us)
Web Interface                 http://127.0.0.1:4040
Forwarding                    http://263db807.ngrok.io -> localhost:7071
Forwarding                    https://263db807.ngrok.io -> localhost:7071

Connections                   ttl     opn     rt1     rt5     p50     p90
                              0       0       0.00    0.00    0.00    0.00
```

Vous utiliserez l’URL https://{sous-domaine}.ngrok.io pour votre abonnement Event Grid.

### <a name="run-the-event-grid-trigger-function"></a>Exécuter la fonction de déclenchement Event Grid

L’URL ngrok ne fait l’objet d’aucun traitement spécial de la part d’Event Grid ; votre fonction doit donc être en cours d’exécution en local lors de la création de l’abonnement. À défaut, la réponse de validation ne serait pas envoyée et la création de l’abonnement échouerait.

### <a name="create-a-subscription"></a>Création d’un abonnement

Créez un abonnement Event Grid du type à tester, et donnez-lui votre point de terminaison ngrok suivant ce modèle :

```
https://{subdomain}.ngrok.io/admin/extensions/EventGridExtensionConfig?functionName={methodname}
``` 

Le paramètre `functionName` doit être le nom de la méthode, et non le nom spécifié dans l’attribut `FunctionName`. C’est pourquoi, lorsqu’un projet comporte plusieurs fonctions, celles-ci doivent avoir des noms uniques de méthode (il ne faut pas qu’elles soient toutes nommées `Run`) pour qu’il soit possible de tester les déclencheurs Event Grid en local.

Voici un exemple utilisant Azure CLI :

```
az eventgrid event-subscription create --resource-id /subscriptions/aeb4b7cb-b7cb-b7cb-b7cb-b7cbb6607f30/resourceGroups/eg0122/providers/Microsoft.Storage/storageAccounts/egblobstor0122 --name egblobsub0126 --endpoint https://263db807.ngrok.io/admin/extensions/EventGridExtensionConfig?functionName=EventGridTrigger
```

Pour plus d’informations sur la création d’abonnements, consultez la section [Créer un abonnement](#create-a-subscription) au début de cet article.

### <a name="trigger-an-event"></a>Déclencher un événement

Déclenchez un événement qui génèrera du trafic HTTP sur votre point de terminaison ngrok.  Par exemple, si vous avez créé un abonnement au Stockage Blob, chargez ou supprimez un objet blob.

La fonction de déclenchement Event Grid s’exécute et affiche des journaux de ce type :

![Exemple de journaux d’une fonction de déclenchement Event Grid](media/functions-bindings-event-grid/eg-output.png)

## <a name="use-an-http-trigger-as-an-event-grid-trigger"></a>Utiliser un déclencheur HTTP comme déclencheur Event Grid

Les événements Event Grid sont reçus en tant que requêtes HTTP, ce qui permet de les gérer à l’aide d’un déclencheur HTTP au lieu d’un déclencheur Event Grid, par exemple, afin de mieux maîtriser l’URL de point de terminaison qui appelle la fonction. 

Si vous utilisez un déclencheur HTTP, vous devrez écrire du code pour accomplir les actions que le déclencheur Event Grid effectue automatiquement :

* Envoyer une réponse de validation à une [requête de validation d’abonnement](../event-grid/security-authentication.md#webhook-event-delivery).
* Appeler la fonction une fois par élément du tableau d’événements contenu dans le corps de la requête.

L’exemple de code C# suivant pour un déclencheur HTTP simule le comportement d’un déclencheur Event Grid :

```csharp
[FunctionName("HttpTrigger")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]HttpRequestMessage req,
    TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");

    var messages = await req.Content.ReadAsAsync<JArray>();

    // If the request is for subscription validation, send back the validation code.
    if (messages.Count > 0 && string.Equals((string)messages[0]["eventType"], 
        "Microsoft.EventGrid.SubscriptionValidationEvent", 
        System.StringComparison.OrdinalIgnoreCase))
    {
        log.Info("Validate request received");
        return req.CreateResponse<object>(new
        {
            validationResponse = messages[0]["data"]["validationCode"]
        });
    }

    // The request is not for subscription validation, so it's for one or more events.
    foreach (JObject message in messages)
    {
        // Handle one event.
        EventGridEvent eventGridEvent = message.ToObject<EventGridEvent>();
        log.Info($"Subject: {eventGridEvent.Subject}");
        log.Info($"Time: {eventGridEvent.EventTime}");
        log.Info($"Event data: {eventGridEvent.Data.ToString()}");
    }

    return req.CreateResponse(HttpStatusCode.OK);
}
```

L’exemple de code JavaScript suivant pour un déclencheur HTTP simule le comportement d’un déclencheur Event Grid :

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    var messages = req.body;
    // If the request is for subscription validation, send back the validation code.
    if (messages.length > 0 && messages[0].eventType == "Microsoft.EventGrid.SubscriptionValidationEvent") {
        context.log('Validate request received');
        context.res = { status: 200, body: messages[0].data.validationCode }
    }
    else {
        // The request is not for subscription validation, so it's for one or more events.
        for (var i = 0; i < messages.length; i++) {
            // Handle one event.
            var message = messages[i];
            context.log('Subject: ' + message.subject);
            context.log('Time: ' + message.eventTime);
            context.log('Data: ' + JSON.stringify(message.data));
        }
    }
    context.done();
};
```

Le code de gestion des événements s’intègre à la boucle par le biais du tableau `messages`.

Pour plus d’informations sur l’URL à utiliser pour appeler la fonction en local ou en cas d’exécution dans Azure, consultez la [documentation de référence des liaisons de déclencheurs HTTP](functions-bindings-http-webhook.md). 

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [En savoir plus sur les déclencheurs et les liaisons Azure Functions](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [En savoir plus sur Event Grid](../event-grid/overview.md)
