---
title: "Suivi des opérations personnalisées avec le kit SDK .NET d’Azure Application Insights | Microsoft Docs"
description: "Suivi des opérations personnalisées avec le kit SDK .NET d’Azure Application Insights"
services: application-insights
documentationcenter: .net
author: SergeyKanzhelev
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 06/30/2017
ms.author: sergkanz
ms.openlocfilehash: 5c6f7521614d7c8337ef31fb8102c5715f83a58d
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/12/2018
---
# <a name="track-custom-operations-with-application-insights-net-sdk"></a>Suivi des opérations personnalisées avec le kit SDK .NET d’Application Insights

Les kits SDK d’Application Azure Insights effectuent automatiquement le suivi des appels et demandes HTTP entrants à des services dépendants, par exemple, des requêtes HTTP ou des requêtes SQL. Le suivi et la corrélation des demandes et des dépendances vous offrent une meilleure visibilité de la réactivité et de la fiabilité de l’application entière sur l’ensemble des micro-services qui combinent cette application. 

Il existe une classe de modèles d’application qui ne peuvent pas être pris en charge de façon générique. La surveillance appropriée de ces modèles requiert une instrumentation manuelle du code. Cet article traite de quelques modèles qui peuvent requérir une instrumentation manuelle, tels que le traitement de la file d’attente personnalisé et les tâches en arrière-plan à long terme en cours d’exécution.

Ce document fournit des conseils sur la façon d’effectuer le suivi d’opérations personnalisées avec le kit SDK Application Insights. Cette documentation concerne :

- Application Insights pour .NET (également appelé Kit SDK de base) version 2.4 et versions ultérieures.
- Application Insights pour applications web (exécute ASP.NET) version 2.4 et versions ultérieures.
- Application Insights pour ASP.NET Core version 2.1 et versions ultérieures.

## <a name="overview"></a>Vue d'ensemble
Une opération est un élément de travail logique exécuté par une application. Il a un nom, une heure de début, une durée, un résultat et un contexte d’exécution tel qu’un nom d’utilisateur, des propriétés et un résultat. Si l’opération A a été initiée par l’opération B, l’opération B est alors définie en tant que parent pour A. Une opération ne peut avoir qu’un seul parent, mais il peut avoir de nombreuses opérations enfants. Pour plus d’informations sur les opérations et la corrélation de télémétrie, consultez [Corrélation de télémétrie d’Application Azure Insights](application-insights-correlation.md).

Dans le kit SDK .NET d’Application Insights, l’opération est décrite par la classe abstraite [OperationTelemetry](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/Microsoft.ApplicationInsights/Extensibility/Implementation/OperationTelemetry.cs) et par ses descendants [RequestTelemetry](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/Microsoft.ApplicationInsights/DataContracts/RequestTelemetry.cs) et [DependencyTelemetry](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/Microsoft.ApplicationInsights/DataContracts/DependencyTelemetry.cs).

## <a name="incoming-operations-tracking"></a>Suivi des opérations entrantes 
Le kit SDK web d’Application Insights collecte automatiquement des requêtes HTTP pour les applications ASP.NET qui s’exécutent dans un pipeline IIS et toutes les applications ASP.NET Core. Il existe des solutions prises en charge par la communauté pour les autres plateformes et infrastructures. Toutefois, si l’application n’est prise en charge par aucune solution standard ni prise en charge par la communauté, vous pouvez l’instrumenter manuellement.

Un autre exemple nécessitant un suivi personnalisé est le Worker qui reçoit des éléments de la file d’attente. Pour certaines files d’attente, l’appel visant à ajouter un message à cette file d’attente est comptabilisé en tant que dépendance. Toutefois, l’opération de haut niveau qui décrit le traitement des messages n’est pas collectée automatiquement.

Voyons à présent comment ces opérations pourraient être suivies.

À un niveau élevé, la tâche consiste à créer `RequestTelemetry` et à définir les propriétés connues. Une fois l’opération terminée, vous réalisez un suivi de la télémétrie. L’exemple suivant illustre ce cas de figure.

### <a name="http-request-in-owin-self-hosted-app"></a>Requête HTTP dans une application Owin auto-hébergée
Dans cet exemple, le contexte du suivi est propagé conformément au[ protocole HTTP de corrélation](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md). Attendez-vous à recevoir les en-têtes décrites ici.

```csharp
public class ApplicationInsightsMiddleware : OwinMiddleware
{
    private readonly TelemetryClient telemetryClient = new TelemetryClient(TelemetryConfiguration.Active);
    
    public ApplicationInsightsMiddleware(OwinMiddleware next) : base(next) {}

    public override async Task Invoke(IOwinContext context)
    {
        // Let's create and start RequestTelemetry.
        var requestTelemetry = new RequestTelemetry
        {
            Name = $"{context.Request.Method} {context.Request.Uri.GetLeftPart(UriPartial.Path)}"
        };

        // If there is a Request-Id received from the upstream service, set the telemetry context accordingly.
        if (context.Request.Headers.ContainsKey("Request-Id"))
        {
            var requestId = context.Request.Headers.Get("Request-Id");
            // Get the operation ID from the Request-Id (if you follow the HTTP Protocol for Correlation).
            requestTelemetry.Context.Operation.Id = GetOperationId(requestId);
            requestTelemetry.Context.Operation.ParentId = requestId;
        }

        // StartOperation is a helper method that allows correlation of 
        // current operations with nested operations/telemetry
        // and initializes start time and duration on telemetry items.
        var operation = telemetryClient.StartOperation(requestTelemetry);

        // Process the request.
        try
        {
            await Next.Invoke(context);
        }
        catch (Exception e)
        {
            requestTelemetry.Success = false;
            telemetryClient.TrackException(e);
            throw;
        }
        finally
        {
            // Update status code and success as appropriate.
            if (context.Response != null)
            {
                requestTelemetry.ResponseCode = context.Response.StatusCode.ToString();
                requestTelemetry.Success = context.Response.StatusCode >= 200 && context.Response.StatusCode <= 299;
            }
            else
            {
                requestTelemetry.Success = false;
            }

            // Now it's time to stop the operation (and track telemetry).
            telemetryClient.StopOperation(operation);
        }
    }
    
    public static string GetOperationId(string id)
    {
        // Returns the root ID from the '|' to the first '.' if any.
        int rootEnd = id.IndexOf('.');
        if (rootEnd < 0)
            rootEnd = id.Length;

        int rootStart = id[0] == '|' ? 1 : 0;
        return id.Substring(rootStart, rootEnd - rootStart);
    }
}
```

Le protocole HTTP pour la corrélation déclare également l’en-tête `Correlation-Context`. Toutefois, il est omis ici par souci de simplicité.

## <a name="queue-instrumentation"></a>Instrumentation des files d’attente
Bien qu’il existe un [protocole HTTP de corrélation](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md) pour transmettre les détails de la corrélation avec la requête HTTP, chaque protocole de file d’attente doit définir la façon dont ces détails seront transmis à travers le message de file d’attente. Certains protocoles de file d’attente (par exemple, AMQP) autorisent la transmission de métadonnées supplémentaires, tandis que d’autres (tels que la file d'attente Stockage Azure) nécessitent le contexte à encoder dans la charge utile de message.

### <a name="service-bus-queue"></a>File d’attente Service Bus
Application Insights effectue le suivi des appels de la messagerie Service Bus avec le nouveau [client Microsoft Azure ServiceBus pour .NET](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus/) 3.0.0 et versions ultérieures.
Si vous utilisez le [modèle de gestionnaire de messages](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler) pour traiter les messages, vous avez terminé : tous les appels Service Bus effectués par votre service sont automatiquement suivis et mis en corrélation avec d’autres éléments de télémétrie. Reportez-vous au [suivi du client Service Bus avec Microsoft Application Insights](../service-bus-messaging/service-bus-end-to-end-tracing.md) si vous traitez des messages manuellement.

Si vous utilisez le package [WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/), examinez les exemples-suivants qui montrent comment effectuer le suivi (et la mise en corrélation) des appels à Service Bus, sachant que la file d’attente Service Bus utilise le protocole AMQP et qu’Application Insights n’effectue pas automatiquement le suivi des opérations de file d’attente.
Les identificateurs de corrélation sont transmis aux propriétés du message.

#### <a name="enqueue"></a>Enqueue (empiler)

```csharp
public async Task Enqueue(string payload)
{
    // StartOperation is a helper method that initializes the telemetry item
    // and allows correlation of this operation with its parent and children.
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("enqueue " + queueName);
    operation.Telemetry.Type = "Queue";
    operation.Telemetry.Data = "Enqueue " + queueName;

    var message = new BrokeredMessage(payload);
    // Service Bus queue allows the property bag to pass along with the message.
    // We will use them to pass our correlation identifiers (and other context)
    // to the consumer.
    message.Properties.Add("ParentId", operation.Telemetry.Id);
    message.Properties.Add("RootId", operation.Telemetry.Context.Operation.Id);

    try
    {
        await queue.SendAsync(message);
        
        // Set operation.Telemetry Success and ResponseCode here.
        operation.Telemetry.Success = true;
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        // Set operation.Telemetry Success and ResponseCode here.
        operation.Telemetry.Success = false;
        throw;
    }
    finally
    {
        telemetryClient.StopOperation(operation);
    }
}
```

#### <a name="process"></a>Process
```csharp
public async Task Process(BrokeredMessage message)
{
    // After the message is taken from the queue, create RequestTelemetry to track its processing.
    // It might also make sense to get the name from the message.
    RequestTelemetry requestTelemetry = new RequestTelemetry { Name = "Dequeue " + queueName };

    var rootId = message.Properties["RootId"].ToString();
    var parentId = message.Properties["ParentId"].ToString();
    // Get the operation ID from the Request-Id (if you follow the HTTP Protocol for Correlation).
    requestTelemetry.Context.Operation.Id = rootId;
    requestTelemetry.Context.Operation.ParentId = parentId;

    var operation = telemetryClient.StartOperation(requestTelemetry);

    try
    {
        await ProcessMessage();
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        throw;
    }
    finally
    {
        // Update status code and success as appropriate.
        telemetryClient.StopOperation(operation);
    }
}
```

### <a name="azure-storage-queue"></a>File d’attente de stockage Azure
L’exemple suivant montre comment effectuer le suivi des opérations de [file d’attente de stockage Azure](../storage/queues/storage-dotnet-how-to-use-queues.md) et mettre en corrélation la télémétrie entre le producteur, le consommateur et le stockage Azure. 

La file d’attente de stockage dispose d’une API HTTP. Tous les appels vers la file d’attente sont suivis par le collecteur de dépendance Application Insights pour les requêtes HTTP.
Préparez-vous.Assurez-vous que `Microsoft.ApplicationInsights.DependencyCollector.HttpDependenciesParsingTelemetryInitializer` se trouve dans `applicationInsights.config`. Si ce n’est pas le cas, ajoutez-le par programmation dans [Filtrage et prétraitement dans le kit SDK d’Application Azure Insights](app-insights-api-filtering-sampling.md).

Si vous configurez manuellement Application Insights, assurez-vous de créer et d’initialiser `Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule` de façon semblable au code suivant :
 
```csharp
DependencyTrackingTelemetryModule module = new DependencyTrackingTelemetryModule();

// You can prevent correlation header injection to some domains by adding it to the excluded list.
// Make sure you add a Storage endpoint. Otherwise, you might experience request signature validation issues on the Storage service side.
module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.windows.net");
module.Initialize(TelemetryConfiguration.Active);

// Do not forget to dispose of the module during application shutdown.
```

Vous pouvez également mettre en corrélation l’ID d’opération Application Insights avec l’ID de demande de stockage. Pour plus d’informations sur la définition et obtention d’un client de demande de stockage et un ID de demande de serveur, consultez [Surveiller, diagnostiquer et dépanner Stockage Azure](../storage/common/storage-monitoring-diagnosing-troubleshooting.md#end-to-end-tracing).

#### <a name="enqueue"></a>Enqueue (empiler)
Étant donné que les files d’attente de stockage prennent en charge l’API HTTP, toutes les opérations mettant en jeu la file d’attente sont automatiquement suivies par Application Insights. Dans de nombreux cas, cette instrumentation doit être suffisante. Toutefois, pour mettre en corrélation les traces côté client avec les traces du producteur, vous devez transmettre un contexte de corrélation de façon similaire à la manière utilisée dans le Protocole HTTP pour la corrélation. 

Cet exemple montre comment effectuer le suivi de l’opération `Enqueue`. Vous pouvez :

 - **Mettre en corrélation les nouvelles tentatives (le cas échéant)** : elles ont toutes un parent commun qui est l’opération `Enqueue`. Dans le cas contraire, elles sont comptabilisées en tant qu’enfants de la demande entrante. S’il existe plusieurs demandes logiques pour la file d’attente, il pourrait être difficile de trouver quel appel a généré de nouvelles tentatives.
 - **Mettre en corrélation les journaux de stockage Azure (si nécessaire)** : elles sont mises en corrélation avec la télémétrie Application Insights.

L’opération `Enqueue` est l’enfant d’une opération parent (par exemple, une demande HTTP entrante). L’appel de dépendance HTTP est l’enfant de l’opération `Enqueue` et le petit-enfant de la demande entrante :

```csharp
public async Task Enqueue(CloudQueue queue, string message)
{
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("enqueue " + queue.Name);
    operation.Telemetry.Type = "Queue";
    operation.Telemetry.Data = "Enqueue " + queue.Name;

    // MessagePayload represents your custom message and also serializes correlation identifiers into payload.
    // For example, if you choose to pass payload serialized to JSON, it might look like
    // {'RootId' : 'some-id', 'ParentId' : '|some-id.1.2.3.', 'message' : 'your message to process'}
    var jsonPayload = JsonConvert.SerializeObject(new MessagePayload
    {
        RootId = operation.Telemetry.Context.Operation.Id,
        ParentId = operation.Telemetry.Id,
        Payload = message
    });
    
    CloudQueueMessage queueMessage = new CloudQueueMessage(jsonPayload);

    // Add operation.Telemetry.Id to the OperationContext to correlate Storage logs and Application Insights telemetry.
    OperationContext context = new OperationContext { ClientRequestID = operation.Telemetry.Id};

    try
    {
        await queue.AddMessageAsync(queueMessage, null, null, new QueueRequestOptions(), context);
    }
    catch (StorageException e)
    {
        operation.Telemetry.Properties.Add("AzureServiceRequestID", e.RequestInformation.ServiceRequestID);
        operation.Telemetry.Success = false;
        operation.Telemetry.ResultCode = e.RequestInformation.HttpStatusCode.ToString();
        telemetryClient.TrackException(e);
    }
    finally
    {
        // Update status code and success as appropriate.
        telemetryClient.StopOperation(operation);
    }
}  
```

Pour réduire la quantité de données de télémétrie que votre application signale ou si vous ne souhaitez pas suivre l’opération `Enqueue` pour d’autres raisons, utilisez directement l’API `Activity` :

- Créez (et démarrez) une nouvelle opération `Activity` au lieu de démarrer l’opération Application Insights. Il n’est *pas* nécessaire de lui assigner des propriétés à part le nom d’opération.
- Sérialisez `yourActivity.Id` dans la charge utile du message à la place de `operation.Telemetry.Id`. Vous pouvez également utiliser `Activity.Current.Id`.


#### <a name="dequeue"></a>Dequeue (Enlever de la file d’attente)
Comme avec `Enqueue`, une requête HTTP réelle à la file d’attente de stockage est automatiquement suivie par Application Insights. Toutefois, l’opération `Enqueue` se produit vraisemblablement dans le contexte parent, par exemple un contexte de demande entrant. Les kits SDK d’Application Insights mettent automatiquement en corrélation cette opération (et sa partie HTTP) avec la demande parent et d’autres données de télémétrie signalées dans le même cadre.

L’opération `Dequeue` est compliquée. Le kit SDK d’Application Insights effectue automatiquement le suivi des demandes HTTP. Toutefois, il ne connait pas le contexte de corrélation avant que le message ne soit analysé. Il n’est pas possible de mettre en corrélation la requête HTTP afin d’obtenir le message avec le reste des données de télémétrie.

Dans de nombreux cas, il peut être utile de mettre en corrélation la requête HTTP adressée à la file d’attente avec d’autres traces également. L’exemple suivant illustre son fonctionnement :

```csharp
public async Task<MessagePayload> Dequeue(CloudQueue queue)
{
    var telemetry = new DependencyTelemetry
    {
        Type = "Queue",
        Name = "Dequeue " + queue.Name
    };

    telemetry.Start();

    try
    {
        var message = await queue.GetMessageAsync();

        if (message != null)
        {
            var payload = JsonConvert.DeserializeObject<MessagePayload>(message.AsString);

            // If there is a message, we want to correlate the Dequeue operation with processing.
            // However, we will only know what correlation ID to use after we get it from the message,
            // so we will report telemetry after we know the IDs.
            telemetry.Context.Operation.Id = payload.RootId;
            telemetry.Context.Operation.ParentId = payload.ParentId;

            // Delete the message.
            return payload;
        }
    }
    catch (StorageException e)
    {
        telemetry.Properties.Add("AzureServiceRequestID", e.RequestInformation.ServiceRequestID);
        telemetry.Success = false;
        telemetry.ResultCode = e.RequestInformation.HttpStatusCode.ToString();
        telemetryClient.TrackException(e);
    }
    finally
    {
        // Update status code and success as appropriate.
        telemetry.Stop();
        telemetryClient.Track(telemetry);
    }

    return null;
}
```

#### <a name="process"></a>Process

Dans l’exemple suivant, un message entrant est tracé de la même façon qu’une requête HTTP entrante :

```csharp
public async Task Process(MessagePayload message)
{
    // After the message is dequeued from the queue, create RequestTelemetry to track its processing.
    RequestTelemetry requestTelemetry = new RequestTelemetry { Name = "Dequeue " + queueName };
    // It might also make sense to get the name from the message.
    requestTelemetry.Context.Operation.Id = message.RootId;
    requestTelemetry.Context.Operation.ParentId = message.ParentId;

    var operation = telemetryClient.StartOperation(requestTelemetry);

    try
    {
        await ProcessMessage();
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        throw;
    }
    finally
    {
        // Update status code and success as appropriate.
        telemetryClient.StopOperation(operation);
    }
}
```

De même, les autres opérations de file d’attente peuvent être instrumentées. Une opération d’aperçu doit être instrumentée de façon similaire à un retrait de la file d’attente. L’instrumentation des opérations de gestion de file d’attente n’est pas nécessaire. Application Insights effectue le suivi d’opérations telles que HTTP et, dans la plupart des cas, cela suffit.

Lors de l’instrumentation d’une suppression de message, assurez-vous de définir les identificateurs de l’opération (corrélation). Vous pouvez également utiliser l’API `Activity`. Vous n’avez alors pas besoin de définir des identificateurs d’opérations sur les éléments de télémétrie, car le Kit SDK Application Insights le fait pour vous :

- Créez une nouvelle API `Activity` une fois que vous avez un élément à partir de la file d’attente.
- Utilisez `Activity.SetParentId(message.ParentId)` pour mettre en corrélation les journaux du consommateur et du producteur.
- Démarrez `Activity`.
- Effectuez le suivi des opérations de retrait de file d’attente, de traitement et de suppression à l’aide des programmes d’assistance `Start/StopOperation`. Procédez à partir du même flux de contrôle asynchrone (contexte d’exécution). De cette manière, elles sont correctement mises en corrélation.
- Arrêtez `Activity`.
- Utilisez `Start/StopOperation` ou appelez manuellement la télémétrie `Track`.

### <a name="batch-processing"></a>Traitement par lots
Avec des files d’attente, vous pouvez retirer plusieurs messages de la file d’attente avec une seule demande. Le traitement de ces messages est vraisemblablement indépendant et appartient à différentes opérations logiques. Dans ce cas, il n’est pas possible de mettre en corrélation l’opération `Dequeue` avec un traitement de message particulier.

Chaque traitement de message doit être traité dans son propre flux de contrôle asynchrone. Pour plus d’informations, consultez la section [Suivi des dépendances sortantes](#outgoing-dependencies-tracking).

## <a name="long-running-background-tasks"></a>Tâches en arrière-plan à long terme
Certaines applications démarrent des opérations à long terme qui peuvent être dues à des demandes de l’utilisateur. Du point de vue du traçage/de l’instrumentation, ce n’est pas différent de l’instrumentation des demandes ou des dépendances : 

```csharp
async Task BackgroundTask()
{
    var operation = telemetryClient.StartOperation<RequestTelemetry>(taskName);
    operation.Telemetry.Type = "Background";
    try
    {
        int progress = 0;
        while (progress < 100)
        {
            // Process the task.
            telemetryClient.TrackTrace($"done {progress++}%");
        }
        // Update status code and success as appropriate.
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        // Update status code and success as appropriate.
        throw;
    }
    finally
    {
        telemetryClient.StopOperation(operation);
    }
}
```

Dans cet exemple, `telemetryClient.StartOperation` crée `RequestTelemetry` et remplit le contexte de corrélation. Supposons que vous avez une opération parent qui a été créée par les demandes entrantes qui ont planifié l’opération. Tant que `BackgroundTask` démarre dans le même flux de contrôle asynchrone en tant que demande entrante, elle est mise en corrélation avec cette opération parent. `BackgroundTask` et tous les éléments de télémétrie imbriqués sont automatiquement mis en corrélation avec la demande à son origine, même après la fin de la demande.

Lorsque la tâche démarre à partir du thread en arrière-plan qui n’a pas d’opération (`Activity`) associée, `BackgroundTask` n’a pas de parent. Toutefois, il peut avoir plusieurs opérations imbriquées. Tous les éléments de télémétrie signalés à partir de la tâche sont mis en corrélation avec l’élément `RequestTelemetry` créé dans l’élément `BackgroundTask`.

## <a name="outgoing-dependencies-tracking"></a>Suivi des dépendances sortantes
Vous pouvez effectuer le suivi de votre propre genre de dépendance ou d’une opération qui n’est pas prise en charge par Application Insights.

La méthode `Enqueue` dans la file d’attente Service Bus ou la file d’attente de stockage peut servir d’exemples pour ce type de suivi personnalisé.

L’approche générale utilisée pour le suivi personnalisé des dépendances est la suivante :

- Appelez la méthode `TelemetryClient.StartOperation` (extension) qui remplit les propriétés `DependencyTelemetry` nécessaires pour la corrélation et d’autres propriétés (heure de début, durée).
- Définissez d’autres propriétés personnalisées sur l’élément `DependencyTelemetry`, comme le nom et tout autre contexte dont vous avez besoin.
- Effectuez un appel de dépendance et attendez les résultats.
- Arrêtez l’opération avec `StopOperation` lorsqu’elle est terminée.
- Traitez les exceptions.

```csharp
public async Task RunMyTaskAsync()
{
    using (var operation = telemetryClient.StartOperation<DependencyTelemetry>("task 1"))
    {
        try 
        {
            var myTask = await StartMyTaskAsync();
            // Update status code and success as appropriate.
        }
        catch(...) 
        {
            // Update status code and success as appropriate.
        }
    }
}
```

L’opération de suppression entraîne l’arrêt de l’opération. Vous pouvez donc l’utiliser au lieu d’appeler `StopOperation`.

*Avertissement* : dans certains cas, une exception non prise en charge peut [empêcher](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/try-finally) l’appel de `finally`, et par conséquent le suivi des opérations.

### <a name="parallel-operations-processing-and-tracking"></a>Suivi et traitement d’opérations parallèles

`StopOperation` ne peut arrêter que l’opération lancée. Si l’opération en cours d’exécution actuelle ne correspond pas à celle que vous souhaitez arrêter, `StopOperation` ne fait rien. Cette situation peut se produire si vous démarrez plusieurs opérations en parallèle dans le même contexte d’exécution :

```csharp
var firstOperation = telemetryClient.StartOperation<DependencyTelemetry>("task 1");
var firstOperation = telemetryClient.StartOperation<DependencyTelemetry>("task 1");
var firstTask = RunMyTaskAsync();

var secondOperation = telemetryClient.StartOperation<DependencyTelemetry>("task 2");
var secondTask = RunMyTaskAsync();

await firstTask;

// FAILURE!!! This will do nothing and will not report telemetry for the first operation
// as currently secondOperation is active.
telemetryClient.StopOperation(firstOperation); 

await secondTask;
```

Veillez à toujours appeler `StartOperation` et à traiter l’opération à l’aide de la même méthode **asynchrone** pour isoler les opérations exécutées en parallèle. Si l’opération est synchrone (et non asynchrone), incluez le processus dans un wrapper et effectuez le suivi avec `Task.Run` :

```csharp
public void RunMyTask(string name)
{
    using (var operation = telemetryClient.StartOperation<DependencyTelemetry>(name))
    {
        Process();
        // Update status code and success as appropriate.
    }
}

public async Task RunAllTasks()
{
    var task1 = Task.Run(() => RunMyTask("task 1"));
    var task2 = Task.Run(() => RunMyTask("task 2"));
    
    await Task.WhenAll(task1, task2);
}
```

## <a name="next-steps"></a>Étapes suivantes

- Découvrez les bases de la [corrélation de télémétrie](application-insights-correlation.md) dans Application Insights.
- Pour connaître les types et les modèles de données Application Insights, consultez [Modèle de données](application-insights-data-model.md).
- Signalez les [événements et métriques](app-insights-api-custom-events-metrics.md) à Application Insights .
- Découvrez la [configuration](app-insights-configuration-with-applicationinsights-config.md#telemetry-initializers-aspnet) standard de la collection de propriétés de contexte.
- Consultez le [Guide de l’utilisateur System.Diagnostics.Activity](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) pour voir comment mettre en corrélation les données de télémétrie.