---
title: "Utilisation de bibliothèques de classes .NET avec Azure Functions | Microsoft Docs"
description: "Apprendre à créer des bibliothèques de classes .NET à utiliser avec Azure Functions"
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: "azure functions, fonctions, traitement des événements, calcul dynamique, architecture sans serveur"
ms.assetid: 9f5db0c2-a88e-4fa8-9b59-37a7096fc828
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/10/2017
ms.author: glenga
ms.openlocfilehash: a856c3b04dc458c0f1e0017066c35edd743565fd
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/23/2017
---
# <a name="using-net-class-libraries-with-azure-functions"></a>Utilisation de bibliothèques de classes .NET avec Azure Functions

En plus des fichiers de script, Azure Functions prend en charge la publication d’une bibliothèque de classes en tant qu’implémentation pour une ou plusieurs fonctions. Nous vous recommandons d’utiliser les [Outils d’Azure Functions pour Visual Studio 2017](https://blogs.msdn.microsoft.com/webdev/2017/05/10/azure-function-tools-for-visual-studio-2017/).

## <a name="prerequisites"></a>Composants requis 

Cet article nécessite les éléments suivants :

- [Visual Studio 2017 version 15.3](https://www.visualstudio.com/vs/) ou version ultérieure.
- Installez la charge de travail de **développement Azure**.

## <a name="functions-class-library-project"></a>Projet de bibliothèque de classes Azure Functions

À partir de Visual Studio, créez un projet Azure Functions. Le nouveau modèle de projet crée les fichiers *host.json* et *local.settings.json*. Vous pouvez [personnaliser les paramètres d’exécution d’Azure Functions dans host.json](functions-host-json.md). 

Le fichier *local.settings.json* stocke des paramètres d’application, des chaînes de connexion et des paramètres pour les outils principaux d’Azure Functions. Pour en savoir plus sur sa structure, voir [Coder et tester Azure Functions localement](functions-run-local.md#local-settings-file).

### <a name="functionname-attribute"></a>Attribut FunctionName

L’attribut [`FunctionNameAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/FunctionNameAttribute.cs) marque une méthode comme un point d’entrée de la fonction. Il doit être utilisé avec un seul déclencheur et 0 ou plusieurs liaisons d’entrée et de sortie.

### <a name="conversion-to-functionjson"></a>Conversion en function.json

Lorsque vous générez un projet Azure Functions, un fichier *function.json* est créé dans le répertoire de la fonction. Le nom de ce répertoire est identique à celui de la fonction que l’attribut `[FunctionName]` spécifie. Le fichier *function.json* contient des déclencheurs et des liaisons, et pointe vers le fichier d’assembly du projet.

Cette conversion est effectuée par le package NuGet [Microsoft\.NET\.Sdk\.Functions](http://www.nuget.org/packages/Microsoft.NET.Sdk.Functions). La source est disponible dans le référentiel GitHub [azure\-functions\-vs\-build\-sdk](https://github.com/Azure/azure-functions-vs-build-sdk).

## <a name="triggers-and-bindings"></a>Déclencheurs et liaisons 

Le tableau suivant répertorie les déclencheurs et liaisons disponibles dans un projet de bibliothèque de classes Azure Functions. Tous les attributs figurent dans l’espace de noms `Microsoft.Azure.WebJobs`.

| Liaison | Attribut | Package NuGet |
|------   | ------    | ------        |
| [Déclencheur, entrée, sortie de Stockage Blob](#blob-storage) | [BlobAttribute], [StorageAccountAttribute] | [Microsoft.Azure.WebJobs] | [Stockage d’objets blob] |
| [Déclencheur Cosmos DB](#cosmos-db) | [CosmosDBTriggerAttribute] | [Microsoft.Azure.WebJobs.Extensions.DocumentDB] | 
| [Entrée et sortie Cosmos DB](#cosmos-db) | [DocumentDBAttribute] | [Microsoft.Azure.WebJobs.Extensions.DocumentDB] |
| [Déclencheur et sortie d’Event Hubs](#event-hub) | [EventHubTriggerAttribute], [EventHubAttribute] | [Microsoft.Azure.WebJobs.ServiceBus] |
| [Entrée et sortie de fichier externe](#api-hub) | [ApiHubFileAttribute] | [Microsoft.Azure.WebJobs.Extensions.ApiHub] |
| [Déclencheur HTTP et webhook](#http) | [HttpTriggerAttribute] | [Microsoft.Azure.WebJobs.Extensions.Http] |
| [Entrée et sortie de Mobile Apps](#mobile-apps) | [MobileTableAttribute] | [Microsoft.Azure.WebJobs.Extensions.MobileApps] | 
| [Sortie de Notification Hubs](#nh) | [NotificationHubAttribute] | [Microsoft.Azure.WebJobs.Extensions.NotificationHubs] | 
| [Déclencheur et sortie de Stockage File d’attente](#queue) | [QueueAttribute], [StorageAccountAttribute] | [Microsoft.Azure.WebJobs] | 
| [Sortie de SendGrid](#sendgrid) | [SendGridAttribute] | [Microsoft.Azure.WebJobs.Extensions.SendGrid] | 
| [Déclencheur et sortie de Service Bus](#service-bus) | [ServiceBusAttribute], [ServiceBusAccountAttribute] | [Microsoft.Azure.WebJobs.ServiceBus]
| [Entrée et sortie de Stockage Table](#table) | [TableAttribute], [StorageAccountAttribute] | [Microsoft.Azure.WebJobs] | 
| [Déclencheur de minuteur](#timer) | [TimerTriggerAttribute] | [Microsoft.Azure.WebJobs.Extensions] | 
| [Sortie de Twilio](#twilio) | [TwilioSmsAttribute] | [Microsoft.Azure.WebJobs.Extensions.Twilio] | 

<a name="blob-storage"></a>

### <a name="blob-storage-trigger-input-bindings-and-output-bindings"></a>Liaisons de sortie, liaisons d’entrée et déclencheur de Stockage Blob

Azure Functions prend en charge les liaisons de déclencheur, d’entrée et de sortie pour Stockage Blob Azure. Pour plus d’informations sur les expressions et métadonnées de liaison, voir [Liaisons de Stockage Blob Azure Functions](functions-bindings-storage-blob.md).

Un déclencheur d’objet blob est défini avec l’attribut `[BlobTrigger]`. Vous pouvez utiliser l’attribut `[StorageAccount]` pour définir le nom de paramètre d’application contenant la chaîne de connexion au compte de stockage utilisé par une classe ou fonction entière.

```csharp
[StorageAccount("AzureWebJobsStorage")]
[FunctionName("BlobTriggerCSharp")]        
public static void Run([BlobTrigger("samples-workitems/{name}")] Stream myBlob, string name, TraceWriter log)
{
    log.Info($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

L’entrée et la sortie d’objet blob sont définies à l’aide de l’attribut `[Blob]`, ainsi qu’avec un paramètre `FileAccess` indiquant la lecture ou l’écriture. L’exemple suivant utilise une liaison de déclencheur d’objet blob et de sortie d’objet blob.

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

<a name="cosmos-db"></a>

### <a name="cosmos-db-trigger-input-bindings-and-output-bindings"></a>Liaisons de sortie, liaisons d’entrée et déclencheur Cosmos DB

Azure Functions prend en charge des déclencheurs et des liaisons d’entrée et de sortie pour Cosmos DB. Pour en savoir plus sur les fonctionnalités de la liaison Cosmos DB, voir [Liaisons Cosmos DB Azure Functions](functions-bindings-documentdb.md).

Pour effectuer le déclenchement à partir d’un document Cosmos DB, utilisez l’attribut `[CosmosDBTrigger]` dans le package NuGet [Microsoft.Azure.WebJobs.Extensions.DocumentDB]. L’exemple suivant effectue un déclenchement à partir de paramètres `database` et `collection` spécifiques. Le paramètre `myCosmosDB` inclut la connexion à l’instance Cosmos DB. 

```csharp
[FunctionName("DocumentUpdates")]
public static void Run(
    [CosmosDBTrigger("database", "collection", ConnectionStringSetting = "myCosmosDB")]
IReadOnlyList<Document> documents, TraceWriter log)
{
        log.Info("Documents modified " + documents.Count);
        log.Info("First document Id " + documents[0].Id);
}
```

Pour lier à un document Cosmos DB, utilisez l’attribut `[DocumentDB]` dans le package NuGet [Microsoft.Azure.WebJobs.Extensions.DocumentDB]. L’exemple suivant comprend un déclencheur de file d’attente et une liaison de sortie d’API DocumentDB.

```csharp
[FunctionName("QueueToDocDB")]        
public static void Run(
    [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem, 
    [DocumentDB("ToDoList", "Items", Id = "id", ConnectionStringSetting = "myCosmosDB")] out dynamic document)
{
    document = new { Text = myQueueItem, id = Guid.NewGuid() };
}

```

<a name="event-hub"></a>

### <a name="event-hubs-trigger-and-output"></a>Déclencheur et sortie d’Event Hubs

Azure Functions prend en charge des liaisons de déclencheur et de sortie pour des Event Hubs. Pour plus d’informations, voir [Liaisons d’Event Hub Azure Functions](functions-bindings-event-hubs.md).

Les types `[Microsoft.Azure.WebJobs.ServiceBus.EventHubTriggerAttribute]` et `[Microsoft.Azure.WebJobs.ServiceBus.EventHubAttribute]` sont définis dans le package NuGet [Microsoft.Azure.WebJobs.ServiceBus]. 

L’exemple suivant utilise un Déclencheur Event Hubs :

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnection")] string myEventHubMessage, TraceWriter log)
{
    log.Info($"C# Event Hub trigger function processed a message: {myEventHubMessage}");
}
```

L’exemple suivant comprend un sortie d’Event Hubs, utilisant la valeur renvoyée par la méthode en tant que sortie :

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnection")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, TraceWriter log)
{
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
    return $"{DateTime.Now}";
}
```

<a name="api-hub"></a>

### <a name="external-file-input-and-output"></a>Entrée et sortie de fichier externe

Azure Functions prend en charge les liaisons de déclencheur, d’entrée et de sortie pour des fichiers externes, tels que Google Drive, Dropbox et OneDrive. Pour plus d’informations, voir [Liaisons de fichiers externes Azure Functions](functions-bindings-external-file.md). Les attributs `[ExternalFileTrigger]` et `[ExternalFile]` sont définis dans le package NuGet [Microsoft.Azure.WebJobs.Extensions.ApiHub].

L’exemple C# suivant présente une liaison d’entrée et de sortie d’un fichier externe. Le code copie le fichier d’entrée dans le fichier de sortie.

```csharp
[StorageAccount("MyStorageConnection")]
[FunctionName("ExternalFile")]
[return: ApiHubFile("MyFileConnection", "samples-workitems/{queueTrigger}-Copy", FileAccess.Write)]
public static string Run([QueueTrigger("myqueue-items")] string myQueueItem, 
    [ApiHubFile("MyFileConnection", "samples-workitems/{queueTrigger}", FileAccess.Read)] string myInputFile, 
    TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    return myInputFile;
}
```

<a name="http"></a>

### <a name="http-and-webhooks"></a>HTTP et webhooks

Utilisez l’attribut `HttpTrigger` pour définir un déclencheur ou webhook HTTP. Cet attribut est défini dans le package NuGet [Microsoft.Azure.WebJobs.Extensions.Http]. Vous pouvez personnaliser le niveau d’autorisation, le type de webhook, l’itinéraire et les méthodes. L’exemple suivant définit un déclencheur HTTP avec une authentification anonyme et le type de webhook _genericJson_.

```csharp
[FunctionName("HttpTriggerCSharp")]
public static HttpResponseMessage Run([HttpTrigger(AuthorizationLevel.Anonymous, WebHookType = "genericJson")] HttpRequestMessage req)
{
    return req.CreateResponse(HttpStatusCode.OK);
}
```

<a name="mobile-apps"></a>

### <a name="mobile-apps-input-and-output"></a>Entrée et sortie de Mobile Apps

Azure Functions prend en charge des liaisons d’entrée et de sortie pour Mobile Apps. Pour plus d’informations, voir [Liaisons Azure Mobile Apps Azure Functions](functions-bindings-mobile-apps.md). L’attribut `[MobileTable]` est défini dans le package NuGet [Microsoft.Azure.WebJobs.Extensions.MobileApps].

L’exemple suivant montre une liaison de sortie Mobile Apps :

```csharp
[FunctionName("MobileAppsOutput")]        
[return: MobileTable(ApiKeySetting = "MyMobileAppKey", TableName = "MyTable", MobileAppUriSetting = "MyMobileAppUri")]
public static object Run([QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem, TraceWriter log)
{
    return new { Text = $"I'm running in a C# function! {myQueueItem}" };
}
```

<a name="nh"></a>

### <a name="notification-hubs-output"></a>Sortie de Notification Hubs

Azure Functions prend en charge une liaison de sortie pour Notification Hubs. Pour plus d’informations, voir [Liaison de sortie Notification Hubs Azure Functions](functions-bindings-notification-hubs.md). L’attribut `[NotificationHub]` est défini dans le package NuGet [Microsoft.Azure.WebJobs.Extensions.NotificationHubs].

<a name="queue"></a>

### <a name="queue-storage-trigger-and-output"></a>Déclencheur et sortie de Stockage File d’attente

Azure Functions prend en charge les liaisons de déclencheur et de sortie pour les files d’attente Azure. Pour plus d’informations, voir [Liaisons de Stockage File d’attente Azure Functions](functions-bindings-storage-queue.md).

L’exemple suivant montre comment utiliser le type de retour de la fonction avec une liaison de sortie de file d’attente en utilisant l’attribut `[Queue]`. 

```csharp
[StorageAccount("AzureWebJobsStorage")]
public static class QueueFunctions
{
    // HTTP trigger with queue output binding
    [FunctionName("QueueOutput")]
    [return: Queue("myqueue-items")]
    public static string QueueOutput([HttpTrigger] dynamic input,  TraceWriter log)
    {
        log.Info($"C# function processed: {input.Text}");
        return input.Text;
    }
}

```

Pour définir un déclencheur de file d’attente, utilisez l’attribut `[QueueTrigger]`.
```csharp
[StorageAccount("AzureWebJobsStorage")]
public static class QueueFunctions
{
    // Queue trigger
    [FunctionName("QueueTrigger")]
    [StorageAccount("AzureWebJobsStorage")]
    public static void QueueTrigger([QueueTrigger("myqueue-items")] string myQueueItem, TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
}

```


<a name="sendgrid"></a>

### <a name="sendgrid-output"></a>Sortie de SendGrid

Azure Functions prend en charge une liaison de sortie SendGrid pour l’envoi de courrier par programmation. Pour plus d’informations, voir [Liaisons SendGrid dans Azure Functions](functions-bindings-sendgrid.md).

L’attribut `[SendGrid]` est défini dans le package NuGet [Microsoft.Azure.WebJobs.Extensions.SendGrid]. Une liaison SendGrid nécessite un paramètre d’application nommé `AzureWebJobsSendGridApiKey`, contenant la clé de votre API SendGrid. Il s’agit du nom du paramètre par défaut de la clé de votre API SendGrid. S’il vous faut plusieurs clés SendGrid, ou si vous devez choisir un nom de paramètre différent, vous pouvez définir ce nom à l’aide de la propriété `ApiKey` de l’attribut de liaison `SendGrid`, comme indiqué ci-dessous :

    [SendGrid(ApiKey = "MyCustomSendGridKeyName")]

Voici un exemple d’utilisation d’une liaison de déclencheur de file d’attente Service Bus et d’une liaison de sortie SendGrid à l’aide de `SendGridMessage` :

```csharp
[FunctionName("SendEmail")]
public static void Run(
    [ServiceBusTrigger("myqueue", AccessRights.Manage, Connection = "ServiceBusConnection")] OutgoingEmail email,
    [SendGrid] out SendGridMessage message)
{
    message = new SendGridMessage();
    message.AddTo(email.To);
    message.AddContent("text/html", email.Body);
    message.SetFrom(new EmailAddress(email.From));
    message.SetSubject(email.Subject);
}

public class OutgoingEmail
{
    public string To { get; set; }
    public string From { get; set; }
    public string Subject { get; set; }
    public string Body { get; set; }
}
```
Remarque : cet exemple nécessite le stockage de la clé d’API SendGrid dans un paramètre d’application nommé `AzureWebJobsSendGridApiKey`.

<a name="service-bus"></a>

### <a name="service-bus-trigger-and-output"></a>Déclencheur et sortie de Service Bus

Azure Functions prend en charge les liaisons de déclencheur et de sortie pour les files d’attente et les rubriques Service Bus. Pour plus d’informations sur la configuration des liaisons, voir [Liaisons Service Bus Azure Functions](functions-bindings-service-bus.md).

Les attributs `[ServiceBusTrigger]` et `[ServiceBus]` sont définis dans le package NuGet [Microsoft.Azure.WebJobs.ServiceBus]. 

Voici un exemple de déclencheur de file d’attente Service Bus :

```csharp
[FunctionName("ServiceBusQueueTriggerCSharp")]                    
public static void Run([ServiceBusTrigger("myqueue", AccessRights.Manage, Connection = "ServiceBusConnection")] string myQueueItem, TraceWriter log)
{
    log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
}
```

L’exemple suivant illustre une liaison de sortie Service Bus utilisant le type de retour de la méthode en tant que sortie :

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string ServiceBusOutput([HttpTrigger] dynamic input, TraceWriter log)
{
    log.Info($"C# function processed: {input.Text}");
    return input.Text;
}
```        

<a name="table"></a>

### <a name="table-storage-input-and-output"></a>Entrée et sortie de Stockage Table

Azure Functions prend en charge les liaisons d’entrée et de sortie pour Stockage de table Azure. Pour plus d’informations, voir [Liaisons de Stockage Table Azure Functions](functions-bindings-storage-table.md).

L’exemple suivant présente une classe avec deux fonctions, qui montre des liaisons de sortie et d’entrée de Stockage Table. 

```csharp
[StorageAccount("AzureWebJobsStorage")]
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

    // use the metadata parameter "queueTrigger" to bind the queue payload
    [FunctionName("TableInput")]
    public static void TableInput([QueueTrigger("table-items")] string input, [Table("MyTable", "Http", "{queueTrigger}")] MyPoco poco, TraceWriter log)
    {
        log.Info($"C# function processed: {poco.Text}");
    }
}

```

<a name="timer"></a>

### <a name="timer-trigger"></a>Déclencheur de minuteur

Azure Functions offre une liaison de déclencheur de minuteur qui vous permet d’exécuter votre code de fonction selon une planification définie. Pour en savoir plus sur les fonctionnalités de la liaison, voir [Déclencheur de minuteur Azure Functions](functions-bindings-timer.md).

Sur le plan Consommation, vous pouvez définir des planifications avec une [expression CRON](http://en.wikipedia.org/wiki/Cron#CRON_expression). Si vous utilisez un plan App Service, vous pouvez également utiliser une chaîne TimeSpan. 

L’exemple suivant définit un déclencheur de minuteur qui s’exécute toutes les cinq minutes :

```csharp
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, TraceWriter log)
{
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
}
```

<a name="twilio"></a>

### <a name="twilio-output"></a>Sortie de Twilio

Azure prend en charge les liaisons de sortie de Twilio pour permettre à vos fonctions d’envoyer des messages texte par SMS. Pour plus d’informations, voir [Envoi de messages SMS depuis Azure Functions à l’aide de la liaison de sortie Twilio](functions-bindings-twilio.md). 

L’attribut `[TwilioSms]` est défini dans le package [Microsoft.Azure.WebJobs.Extensions.Twilio].

L’exemple C# suivant utilise un déclencheur de file d’attente et une liaison de sortie Twilio :

```csharp
[FunctionName("QueueTwilio")]
[return: TwilioSms(AccountSidSetting = "TwilioAccountSid", AuthTokenSetting = "TwilioAuthToken", From = "+1425XXXXXXX" )]
public static SMSMessage Run([QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] JObject order, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {order}");

    var message = new SMSMessage()
    {
        Body = $"Hello {order["name"]}, thanks for your order!",
        To = order["mobileNumber"].ToString()
    };

    return message;
}
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’utilisation d’Azure Functions dans un script C#, voir la référence du développeur de script [Azure Functions C\#](functions-reference-csharp.md).

[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]


<!-- NuGet packages --> 
[Microsoft.Azure.WebJobs]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs/2.1.0-beta1
[Microsoft.Azure.WebJobs.Extensions.DocumentDB]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DocumentDB/1.1.0-beta4
[Microsoft.Azure.WebJobs.ServiceBus]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus/2.1.0-beta1
[Microsoft.Azure.WebJobs.Extensions.MobileApps]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MobileApps/1.1.0-beta1
[Microsoft.Azure.WebJobs.Extensions.NotificationHubs]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.NotificationHubs/1.1.0-beta1
[Microsoft.Azure.WebJobs.ServiceBus]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus/2.1.0-beta1
[Microsoft.Azure.WebJobs.Extensions.SendGrid]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SendGrid/2.1.0-beta1
[Microsoft.Azure.WebJobs.Extensions.Http]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Http/1.0.0-beta1
[Microsoft.Azure.WebJobs.Extensions.BotFramework]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.BotFramework/1.0.15-beta
[Microsoft.Azure.WebJobs.Extensions.ApiHub]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ApiHub/1.0.0-beta4
[Microsoft.Azure.WebJobs.Extensions.Twilio]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Twilio/1.1.0-beta1
[Microsoft.Azure.WebJobs.Extensions]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions/2.1.0-beta1


<!-- Links to source --> 
[DocumentDBAttribute]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.DocumentDB/DocumentDBAttribute.cs
[CosmosDBTriggerAttribute]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.DocumentDB/Trigger/CosmosDBTriggerAttribute.cs
[EventHubAttribute]: https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs
[EventHubTriggerAttribute]: https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubTriggerAttribute.cs
[MobileTableAttribute]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs
[NotificationHubAttribute]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.NotificationHubs/NotificationHubAttribute.cs 
[ServiceBusAttribute]: https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAttribute.cs
[ServiceBusAccountAttribute]: https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs
[QueueAttribute]: https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueAttribute.cs
[StorageAccountAttribute]: https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs
[BlobAttribute]: https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs
[TableAttribute]: https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TableAttribute.cs
[TwilioSmsAttribute]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/TwilioSMSAttribute.cs
[SendGridAttribute]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.SendGrid/SendGridAttribute.cs
[HttpTriggerAttribute]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions.Http/HttpTriggerAttribute.cs
[ApiHubFileAttribute]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.ApiHub/ApiHubFileAttribute.cs
[TimerTriggerAttribute]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerTriggerAttribute.cs
