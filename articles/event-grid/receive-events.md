---
title: "Recevoir des événements d’Azure Event Grid sur un point de terminaison HTTP"
description: "Décrit comment valider un point de terminaison HTTP, puis recevoir et désérialiser des événements d’Azure Event Grid"
services: event-grid
author: banisadr
manager: darosa
ms.service: event-grid
ms.topic: article
ms.date: 02/16/2018
ms.author: babanisa
ms.openlocfilehash: 179f7c46186762eed2f7f8ac90620ac2fec9caf3
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/24/2018
---
# <a name="receive-events-to-an-http-endpoint"></a>Recevoir des événements sur un point de terminaison HTTP

Cet article décrit comment [valider un point de terminaison HTTP](security-authentication.md#webhook-event-delivery) pour recevoir des événements à partir d’un abonnement à des événements, puis désérialiser des événements. Cet article utilise une fonction d’Azure à des fins de démonstration, mais les mêmes concepts s’appliquent quel que soit l’emplacement où l’application est hébergée.

> [!NOTE]
> Il est **fortement** recommandé d’utiliser un [déclencheur Event Grid](../azure-functions/functions-bindings-event-grid.md) lors du déclenchement d’une fonction d’Azure avec Event Grid. L’utilisation d’un déclencheur Webhook générique ici est démonstrative.

## <a name="prerequisites"></a>configuration requise

* Vous aurez besoin d’une application de fonction avec une [fonction déclenchée via HTTP](../azure-functions/functions-create-generic-webhook-triggered-function.md).

## <a name="add-dependencies"></a>Ajout de dépendances

Si vous développez dans .Net, [ajoutez une dépendance](../azure-functions/functions-reference-csharp.md#referencing-custom-assemblies) à votre fonction pour le `Microsoft.Azure.EventGrid` [package Nuget](https://www.nuget.org/packages/Microsoft.Azure.EventGrid). Des Kits de développement logiciel (SDK) pour d’autres langues sont disponibles via la référence [Kits SDK de publication](./sdk-overview.md#publish-sdks). Ces packages contiennent les modèles des types d’événement natif comme `EventGridEvent`, `StorageBlobCreatedEventData` et `EventHubCaptureFileCreatedEventData`.

Pour ce faire, cliquez sur le lien « Afficher les fichiers » dans votre fonction Azure (volet le plus à droite dans le portail des fonctions Azure) et créez un fichier appelé project.json. Ajoutez le code suivant au fichier `project.json` et enregistrez-le :

 ```json
{
  "frameworks": {
    "net46":{
      "dependencies": {
        "Microsoft.Azure.EventGrid": "1.1.0-preview"
      }
    }
   }
}

```

![Package NuGet ajouté](./media/receive-events/add-dependencies.png)

## <a name="endpoint-validation"></a>Validation de point de terminaison

La première chose à faire est de traiter les événements `Microsoft.EventGrid.SubscriptionValidationEvent`. Chaque fois qu’un nouvel abonnement à des événements est créé, Event Grid envoie un événement de validation au point de terminaison avec un `validationCode` dans la charge utile de données. Le point de terminaison est nécessaire pour reproduire cela dans le corps de réponse pour [prouver que le point de terminaison est valide et qu’il vous appartient](security-authentication.md#webhook-event-delivery). Si vous utilisez un [déclencheur Event Grid](../azure-functions/functions-bindings-event-grid.md) au lieu d’une fonction déclenchée par un Webhook, la validation du point de terminaison est traitée pour vous.

Pour traiter la validation d’abonnement, utilisez le code suivant :

```csharp

using System.Net;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using Newtonsoft.Json.Serialization;
using Microsoft.Azure.EventGrid.Models;

class SubscriptionValidationEventData
{
    public string ValidationCode { get; set; }
}

class SubscriptionValidationResponseData
{
    public string ValidationResponse { get; set; }
}

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
{

    log.Info($"C# HTTP trigger function begun");
    string response = string.Empty;
    const string SubscriptionValidationEvent = "Microsoft.EventGrid.SubscriptionValidationEvent";

    string requestContent = await req.Content.ReadAsStringAsync();
    EventGridEvent[] eventGridEvents = JsonConvert.DeserializeObject<EventGridEvent[]>(requestContent);

    foreach (EventGridEvent eventGridEvent in eventGridEvents)
    {
        JObject dataObject = eventGridEvent.Data as JObject;

        // Deserialize the event data into the appropriate type based on event type
        if (string.Equals(eventGridEvent.EventType, SubscriptionValidationEvent, StringComparison.OrdinalIgnoreCase))
        {
            var eventData = dataObject.ToObject<SubscriptionValidationEventData>();
            log.Info($"Got SubscriptionValidation event data, validation code: {eventData.ValidationCode}, topic: {eventGridEvent.Topic}");
            // Do any additional validation (as required) and then return back the below response
            var responseData = new SubscriptionValidationResponseData();
            responseData.ValidationResponse = eventData.ValidationCode;
            return req.CreateResponse(HttpStatusCode.OK, responseData);
        }
    }

    return req.CreateResponse(HttpStatusCode.OK, response);
}

```

```javascript

var http = require('http');

module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function begun');
    var validationEventType = "Microsoft.EventGrid.SubscriptionValidationEvent";

    for (var events in req.body) {
        var body = req.body[events];
        // Deserialize the event data into the appropriate type based on event type
        if (body.data && body.eventType == validationEventType) {
            context.log("Got SubscriptionValidation event data, validation code: " + body.data.validationCode + " topic: " + body.topic);

            // Do any additional validation (as required) and then return back the below response
            var code = body.data.validationCode;
            context.res = { status: 200, body: { "ValidationResponse": code } };
        }
    }
    context.done();
};

```

### <a name="test-validation-response"></a>Tester la réponse de validation

Testez la fonction de réponse de validation en collant l’exemple d’événement dans le champ de test pour la fonction :

```json

[{
  "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
  "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "subject": "",
  "data": {
    "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
  },
  "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
  "eventTime": "2018-01-25T22:12:19.4556811Z",
  "metadataVersion": "1",
  "dataVersion": "1"
}]

```

Lorsque vous cliquez sur Exécuter, la sortie doit être 200 OK et `{"ValidationResponse":"512d38b6-c7b8-40c8-89fe-f46f9e9622b6"}` dans le corps :

![réponse de validation](./media/receive-events/validation-response.png)

## <a name="handle-blob-storage-events"></a>Traiter les événements de stockage Blob

Nous pouvons à présent étendre la fonction pour traiter `Microsoft.Storage.BlobCreated` :

```cs

using System.Net;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using Newtonsoft.Json.Serialization;
using Microsoft.Azure.EventGrid.Models;

class SubscriptionValidationEventData
{
    public string ValidationCode { get; set; }
}

class SubscriptionValidationResponseData
{
    public string ValidationResponse { get; set; }
}

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info($"C# HTTP trigger function begun");
    string response = string.Empty;
    const string SubscriptionValidationEvent = "Microsoft.EventGrid.SubscriptionValidationEvent";
    const string StorageBlobCreatedEvent = "Microsoft.Storage.BlobCreated";

    string requestContent = await req.Content.ReadAsStringAsync();
    EventGridEvent[] eventGridEvents = JsonConvert.DeserializeObject<EventGridEvent[]>(requestContent);

    foreach (EventGridEvent eventGridEvent in eventGridEvents)
    {
        JObject dataObject = eventGridEvent.Data as JObject;

        // Deserialize the event data into the appropriate type based on event type 
        if (string.Equals(eventGridEvent.EventType, SubscriptionValidationEvent, StringComparison.OrdinalIgnoreCase))
        {
            var eventData = dataObject.ToObject<SubscriptionValidationEventData>();
            log.Info($"Got SubscriptionValidation event data, validation code: {eventData.ValidationCode}, topic: {eventGridEvent.Topic}");

            // Do any additional validation (as required) and then return back the below response
            var responseData = new SubscriptionValidationResponseData();
            responseData.ValidationResponse = eventData.ValidationCode;
            return req.CreateResponse(HttpStatusCode.OK, responseData);
        }

        else if (string.Equals(eventGridEvent.EventType, StorageBlobCreatedEvent, StringComparison.OrdinalIgnoreCase))
        {
            var eventData = dataObject.ToObject<StorageBlobCreatedEventData>();
            log.Info($"Got BlobCreated event data, blob URI {eventData.Url}");
        }
    }

    return req.CreateResponse(HttpStatusCode.OK, response);
}

```

```javascript

var http = require('http');

module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function begun');
    var validationEventType = "Microsoft.EventGrid.SubscriptionValidationEvent";
    var storageBlobCreatedEvent = "Microsoft.Storage.BlobCreated";

    for (var events in req.body) {
        var body = req.body[events];
        // Deserialize the event data into the appropriate type based on event type  
        if (body.data && body.eventType == validationEventType) {
            context.log("Got SubscriptionValidation event data, validation code: " + body.data.validationCode + " topic: " + body.topic);

            // Do any additional validation (as required) and then return back the below response
            var code = body.data.validationCode;
            context.res = { status: 200, body: { "ValidationResponse": code } };
        }

        else if (body.data && body.eventType == storageBlobCreatedEvent) {
            var blobCreatedEventData = body.data;
            context.log("Relaying received blob created event payload:" + JSON.stringify(blobCreatedEventData));
        }
    }
    context.done();
};

```

### <a name="test-blob-created-event-handling"></a>Tester le traitement de l’événement Blob Created

Testez la nouvelle fonctionnalité de la fonction en plaçant un [événement de stockage Blob](./event-schema-blob-storage.md#example-event) dans le champ de test en exécutant :

```json

[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/xstoretestaccount",
  "subject": "/blobServices/default/containers/testcontainer/blobs/testfile.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "eTag": "0x8D4BCC2E4835CD0",
    "contentType": "text/plain",
    "contentLength": 524288,
    "blobType": "BlockBlob",
    "url": "https://example.blob.core.windows.net/testcontainer/testfile.txt",
    "sequencer": "00000000000004420000000000028963",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]

```

Vous devez voir la sortie d’URL de blob dans le journal de la fonction :

![Journal de sortie](./media/receive-events/blob-event-response.png)

Vous pouvez également tester cela en direct en créant un compte de stockage Blob ou un compte de stockage à usage général V2 (GPv2), [en ajoutant un abonnement à des événements](../storage/blobs/storage-blob-event-quickstart.md) et en définissant le point de terminaison sur l’URL de la fonction :

![URL de la fonction](./media/receive-events/function-url.png)

## <a name="handle-custom-events"></a>Traiter des événements personnalisés

Enfin, étendons la fonction une fois de plus pour qu’elle puisse également traiter des événements personnalisés. Nous ajoutons une vérification pour notre propre événement `Contoso.Items.ItemReceived`. Votre code final doit ressembler à ceci :

```cs

using System.Net;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using Newtonsoft.Json.Serialization;
using Microsoft.Azure.EventGrid.Models;

class SubscriptionValidationEventData
{
    public string ValidationCode { get; set; }
}

class SubscriptionValidationResponseData
{
    public string ValidationResponse { get; set; }
}

class ContosoItemReceivedEventData
{
    public string id { get; set; }
    public string message { get; set; }
    public string time { get; set; }
}

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info($"C# HTTP trigger function begun");
    string response = string.Empty;
    const string SubscriptionValidationEvent = "Microsoft.EventGrid.SubscriptionValidationEvent";
    const string StorageBlobCreatedEvent = "Microsoft.Storage.BlobCreated";
    const string CustomTopicEvent = "Contoso.Items.ItemReceived";

    string requestContent = await req.Content.ReadAsStringAsync();
    EventGridEvent[] eventGridEvents = JsonConvert.DeserializeObject<EventGridEvent[]>(requestContent);

    foreach (EventGridEvent eventGridEvent in eventGridEvents)
    {
        JObject dataObject = eventGridEvent.Data as JObject;

        // Deserialize the event data into the appropriate type based on event type
        if (string.Equals(eventGridEvent.EventType, SubscriptionValidationEvent, StringComparison.OrdinalIgnoreCase))
        {
            var eventData = dataObject.ToObject<SubscriptionValidationEventData>();
            log.Info($"Got SubscriptionValidation event data, validation code: {eventData.ValidationCode}, topic: {eventGridEvent.Topic}");
            // Do any additional validation (as required) and then return back the below response
            var responseData = new SubscriptionValidationResponseData();
            responseData.ValidationResponse = eventData.ValidationCode;
            return req.CreateResponse(HttpStatusCode.OK, responseData);
        }

        else if (string.Equals(eventGridEvent.EventType, StorageBlobCreatedEvent, StringComparison.OrdinalIgnoreCase))
        {
            var eventData = dataObject.ToObject<StorageBlobCreatedEventData>();
            log.Info($"Got BlobCreated event data, blob URI {eventData.Url}");
        }

        else if (string.Equals(eventGridEvent.EventType, CustomTopicEvent, StringComparison.OrdinalIgnoreCase))
        {
            var eventData = dataObject.ToObject<ContosoItemReceivedEventData>();
            log.Info($"Got ContosoItemReceived event data, item URI {eventData.id}");
        }
    }

    return req.CreateResponse(HttpStatusCode.OK, response);
}

```

```javascript

var http = require('http');
var t = require('tcomb');

module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function begun');
    var validationEventType = "Microsoft.EventGrid.SubscriptionValidationEvent";
    var storageBlobCreatedEvent = "Microsoft.Storage.BlobCreated";
    var customEventType = "Contoso.Items.ItemReceived";
    var ContosoItemReceivedEventData = t.struct({
        id: t.Str,
        message: t.Str,
        time: t.Str,
    })

    for (var events in req.body) {
        var body = req.body[events];
        // Deserialize the event data into the appropriate type based on event type
        if (body.data && body.eventType == validationEventType) {
            context.log("Got SubscriptionValidation event data, validation code: " + body.data.validationCode + " topic: " + body.topic);

            // Do any additional validation (as required) and then return back the below response
            var code = body.data.validationCode;
            context.res = { status: 200, body: { "ValidationResponse": code } };
        }

        else if (body.data && body.eventType == storageBlobCreatedEvent) {
            var blobCreatedEventData = body.data;
            context.log("Relaying received blob created event payload:" + JSON.stringify(blobCreatedEventData));
        }

        else if (body.data && body.eventType == customEventType) {
            var payload = new ContosoItemReceivedEventData(body.data);
            context.log("Relaying received custom payload:" + JSON.stringify(payload));
            context.log(payload instanceof ContosoItemReceivedEventData);
        }
    }
    context.done();
};

```

### <a name="test-custom-event-handling"></a>Tester le traitement des événements personnalisés

Enfin, testez que votre fonction étendue peut maintenant traiter votre type d’événement personnalisé :

```json

[{
    "subject": "Contoso/foo/bar/items",
    "eventType": "Microsoft.EventGrid.CustomEventType",
    "eventTime": "2017-08-16T01:57:26.005121Z",
    "id": "602a88ef-0001-00e6-1233-1646070610ea",
    "data": { 
            "id": "831e1650-001e-001b-66ab-eeb76e069631",
            "message": "Contoso item Foo",
            "time": "2017-06-26T18:41:00.9584103Z"
            },
    "dataVersion": "",
    "metadataVersion": "1"
}]

```

Vous pouvez également tester cette fonctionnalité en direct en [envoyant un événement personnalisé avec CURL à partir du portail](./custom-event-quickstart-portal.md) ou en [publiant vers une rubrique personnalisée](./post-to-custom-topic.md) à l’aide d’un service ou d’une application qui peut PUBLIER sur un point de terminaison tel que [Postman](https://www.getpostman.com/). Créez une rubrique personnalisée et un abonnement à des événements avec le point de terminaison défini en tant qu’URL de la fonction.

## <a name="next-steps"></a>Étapes suivantes

* Explorer les [Kits SDK Event Grid de gestion et de publication](./sdk-overview.md)
* Découvrez comment [publier vers une rubrique personnalisée](./post-to-custom-topic.md)
* Essayez l’un des didacticiels approfondis sur Event Grid et les fonctions tel que [Automatiser le redimensionnement des images chargées à l’aide d’Event Grid](resize-images-on-storage-blob-upload-event.md)
