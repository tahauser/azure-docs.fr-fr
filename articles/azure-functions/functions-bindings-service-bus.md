---
title: Liaisons Azure Service Bus pour Azure Functions
description: Découvrez comment utiliser des déclencheurs et des liaisons Azure Service Bus dans Azure Functions.
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: ''
tags: ''
keywords: azure functions, fonctions, traitement des événements, calcul dynamique, architecture sans serveur
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/01/2017
ms.author: tdykstra
ms.openlocfilehash: 02a34111fbab62884c9ecbfc084a55d21d775182
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/17/2018
---
# <a name="azure-service-bus-bindings-for-azure-functions"></a>Liaisons Azure Service Bus pour Azure Functions

Cet article explique comment utiliser des liaisons Azure Service Bus dans Azure Functions. Azure Functions prend en charge les liaisons de déclencheur et de sortie pour les files d’attente et les rubriques Service Bus.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages"></a>Packages

Les liaisons Service Bus sont fournies dans le package NuGet [Microsoft.Azure.WebJobs.ServiceBus](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus). Le code source pour le package se trouve dans le dépôt GitHub [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/).

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="trigger"></a>Déclencheur

Utilisez le déclencheur Service Bus pour répondre aux messages provenant d'une file d’attente ou d'une rubrique Service Bus. 

## <a name="trigger---example"></a>Déclencheur - exemple

Consultez l’exemple propre à un langage particulier :

* [C#](#trigger---c-example)
* [Script C# (.csx)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [JavaScript](#trigger---javascript-example)

### <a name="trigger---c-example"></a>Déclencheur - exemple C#

L’exemple suivant montre une [fonction C#](functions-dotnet-class-library.md) qui consigne un message de la file d’attente Service Bus.

```cs
[FunctionName("ServiceBusQueueTriggerCSharp")]                    
public static void Run(
    [ServiceBusTrigger("myqueue", AccessRights.Manage, Connection = "ServiceBusConnection")] 
    string myQueueItem, 
    TraceWriter log)
{
    log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
}
```

Cet exemple concerne Azure Functions version 1.x ; pour la version 2.x, [omettez le paramètre des droits d’accès](#trigger---configuration).
 
### <a name="trigger---c-script-example"></a>Déclencheur - exemple Script C#

L’exemple suivant montre une liaison de déclencheur Service Bus dans un fichier *function.json* et une [fonction de script C#](functions-reference-csharp.md) qui utilise la liaison. La fonction consigne un message de la file d’attente Service Bus.

Voici les données de liaison dans le fichier *function.json* :

```json
{
"bindings": [
    {
    "queueName": "testqueue",
    "connection": "MyServiceBusConnection",
    "name": "myQueueItem",
    "type": "serviceBusTrigger",
    "direction": "in"
    }
],
"disabled": false
}
```

Voici le code Script C# :

```cs
public static void Run(string myQueueItem, TraceWriter log)
{
    log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
}
```

### <a name="trigger---f-example"></a>Déclencheur - exemple F#

L’exemple suivant montre une liaison de déclencheur Service Bus dans un fichier *function.json* et une [fonction F#](functions-reference-fsharp.md) qui utilise la liaison. La fonction consigne un message de la file d’attente Service Bus. 

Voici les données de liaison dans le fichier *function.json* :

```json
{
"bindings": [
    {
    "queueName": "testqueue",
    "connection": "MyServiceBusConnection",
    "name": "myQueueItem",
    "type": "serviceBusTrigger",
    "direction": "in"
    }
],
"disabled": false
}
```

Voici le code de script F# :

```fsharp
let Run(myQueueItem: string, log: TraceWriter) =
    log.Info(sprintf "F# ServiceBus queue trigger function processed message: %s" myQueueItem)
```

### <a name="trigger---javascript-example"></a>Déclencheur - exemple JavaScript

L’exemple suivant montre une liaison de déclencheur Service Bus dans un fichier *function.json* et une [fonction JavaScript](functions-reference-node.md) qui utilise la liaison. La fonction consigne un message de la file d’attente Service Bus. 

Voici les données de liaison dans le fichier *function.json* :

```json
{
"bindings": [
    {
    "queueName": "testqueue",
    "connection": "MyServiceBusConnection",
    "name": "myQueueItem",
    "type": "serviceBusTrigger",
    "direction": "in"
    }
],
"disabled": false
}
```

Voici le code de script JavaScript :

```javascript
module.exports = function(context, myQueueItem) {
    context.log('Node.js ServiceBus queue trigger function processed message', myQueueItem);
    context.done();
};
```

## <a name="trigger---attributes"></a>Déclencheur - attributs

Dans les [bibliothèques de classes C#](functions-dotnet-class-library.md), utilisez les attributs suivants pour configurer un déclencheur de Service Bus :

* [ServiceBusTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusTriggerAttribute.cs)

  Le constructeur de l’attribut prend le nom de la file d’attente ou la rubrique et l’abonnement. Dans Azure Functions version 1.x, vous pouvez également spécifier les droits d’accès de la connexion. Si vous ne spécifiez pas de droits d’accès, la valeur par défaut est `Manage`. Pour en savoir plus, consultez la section [Déclencheur - configuration](#trigger---configuration).

  Voici un exemple montrant l’attribut utilisé avec un paramètre de chaîne :

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue")] string myQueueItem, TraceWriter log)
  {
      ...
  }
  ```

  Vous pouvez définir la propriété `Connection` pour spécifier le compte Service Bus à utiliser, comme indiqué dans l’exemple suivant :

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] 
      string myQueueItem, TraceWriter log)
  {
      ...
  }
  ```

  Pour obtenir un exemple complet, consultez [Déclencheur - exemple C#](#trigger---c-example).

* [ServiceBusAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs)

  Fournit une autre manière de spécifier le compte Service Bus à utiliser. Le constructeur prend le nom d’un paramètre d’application comportant une chaîne de connexion Service Bus. L’attribut peut être appliqué au niveau du paramètre, de la méthode ou de la classe. L’exemple suivant montre le niveau de la classe et celui de la méthode :

  ```csharp
  [ServiceBusAccount("ClassLevelServiceBusAppSetting")]
  public static class AzureFunctions
  {
      [ServiceBusAccount("MethodLevelServiceBusAppSetting")]
      [FunctionName("ServiceBusQueueTriggerCSharp")]
      public static void Run(
          [ServiceBusTrigger("myqueue", AccessRights.Manage)] 
          string myQueueItem, TraceWriter log)
  {
      ...
  }
  ```

Le compte Service Bus à utiliser est déterminé dans l’ordre suivant :

* La propriété `Connection` de l’attribut `ServiceBusTrigger`.
* L’attribut `ServiceBusAccount` appliqué au même paramètre que l’attribut `ServiceBusTrigger`.
* L’attribut `ServiceBusAccount` appliqué à la fonction.
* L’attribut `ServiceBusAccount` appliqué à la classe.
* Le paramètre d’application « AzureWebJobsServiceBus ».

## <a name="trigger---configuration"></a>Déclencheur - configuration

Le tableau suivant décrit les propriétés de configuration de liaison que vous définissez dans le fichier *function.json* et l’attribut `ServiceBusTrigger`.

|Propriété function.json | Propriété d’attribut |Description|
|---------|---------|----------------------|
|**type** | n/a | Doit être défini sur « serviceBusTrigger ». Cette propriété est définie automatiquement lorsque vous créez le déclencheur dans le portail Azure.|
|**direction** | n/a | Doit être défini sur « in ». Cette propriété est définie automatiquement lorsque vous créez le déclencheur dans le portail Azure. |
|**name** | n/a | Nom de la variable qui représente le message de la file d’attente ou de la rubrique dans le code de la fonction. Défini sur « $return » pour faire référence à la valeur de retour de la fonction. | 
|**queueName**|**QueueName**|Nom de la file d’attente à surveiller.  Défini uniquement en cas de surveillance d’une file d’attente, ne s’applique pas à une rubrique.
|**topicName**|**TopicName**|Nom de la rubrique à surveiller. Défini uniquement en cas de surveillance d’une rubrique, ne s’applique pas à une file d’attente.|
|**subscriptionName**|**SubscriptionName**|Nom de l’abonnement à surveiller. Défini uniquement en cas de surveillance d’une rubrique, ne s’applique pas à une file d’attente.|
|**Connexion**|**Connection**|Nom d’un paramètre d’application comportant la chaîne de connexion Service Bus à utiliser pour cette liaison. Si le nom du paramètre d’application commence par « AzureWebJobs », vous ne pouvez spécifier que le reste du nom. Par exemple, si vous définissez `connection` sur « MyServiceBus », le runtime Functions recherche un paramètre d’application qui est nommé « AzureWebJobsMyServiceBus ». Si vous laissez `connection` vide, le runtime Functions utilise la chaîne de connexion Service Bus par défaut dans le paramètre d’application nommé « AzureWebJobsServiceBus ».<br><br>Pour obtenir une chaîne de connexion, suivez les étapes indiquées à la section [Obtenir les informations d’identification de gestion](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md#obtain-the-management-credentials). La chaîne de connexion doit être destinée à un espace de noms Service Bus, et non limitée à une file d’attente ou une rubrique spécifique. |
|**accessRights**|**Access**|Droits d’accès de la chaîne de connexion. Les valeurs disponibles sont `manage` et `listen`. La valeur par défaut est `manage`, ce qui indique que `connection` a l'autorisation **Gérer**. Si vous utilisez une chaîne de connexion qui n’a pas l'autorisation **Gérer**, définissez `accessRights` sur « écouter ». Sinon, le runtime Functions pourrait échouer à effectuer des opérations qui nécessitent des droits de gestion. Dans Azure Functions version 2.x, cette propriété n’est pas disponible car la version la plus récente du SDK Stockage ne prend pas en charge les opérations de gestion.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Déclencheur - utilisation

En langage C# et dans un script C#, vous pouvez utiliser les types de paramètres suivants pour le message de la file d’attente ou de la rubrique :

* `string` -Si le message est un texte.
* `byte[]` - Utile pour les données binaires.
* Un type personnalisé - Si le message contient JSON, Azure Functions essaie de désérialiser les données JSON.
* `BrokeredMessage` - Vous donne le message désérialisé avec la méthode [BrokeredMessage.GetBody<T>()](https://msdn.microsoft.com/library/hh144211.aspx).

Dans JavaScript, accédez au message de la file d’attente ou de la rubrique à l’aide de `context.bindings.<name from function.json>`. Le message Service Bus est passé à la fonction en tant que chaîne ou en tant qu’objet JSON.

## <a name="trigger---poison-messages"></a>Déclencheur - messages incohérents

La gestion des messages incohérents ne peut pas être contrôlée ou configurée dans Azure Functions. Service Bus gère lui-même les messages incohérents.

## <a name="trigger---peeklock-behavior"></a>Déclencheur - Comportement de PeekLock

Le runtime Functions reçoit un message en [mode PeekLock](../service-bus-messaging/service-bus-performance-improvements.md#receive-mode). Il appelle l’élément `Complete` sur le message si la fonction se termine correctement. Si la fonction échoue, il appelle l’élément `Abandon`. Si la fonction s’exécute au-delà du délai imparti à `PeekLock`, le verrou est automatiquement renouvelé.

## <a name="trigger---hostjson-properties"></a>Déclencheur - propriétés de host.json

Le fichier [host.json](functions-host-json.md#servicebus) contient les paramètres qui contrôlent le comportement du déclencheur Service Bus.

[!INCLUDE [functions-host-json-event-hubs](../../includes/functions-host-json-service-bus.md)]

## <a name="output"></a>Sortie

Utilisez la liaison de sortie Azure Service Bus pour envoyer des messages de file d’attente ou de rubrique.

## <a name="output---example"></a>Sortie - exemple

Consultez l’exemple propre à un langage particulier :

* [C#](#output---c-example)
* [Script C# (.csx)](#output---c-script-example)
* [F#](#output---f-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>Sortie - exemple C#

L’exemple suivant montre une [fonction C#](functions-dotnet-class-library.md) qui envoie un message de la file d’attente Service Bus :

```cs
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string ServiceBusOutput([HttpTrigger] dynamic input, TraceWriter log)
{
    log.Info($"C# function processed: {input.Text}");
    return input.Text;
}
```

### <a name="output---c-script-example"></a>Sortie - exemple Script C#

L’exemple suivant montre une liaison de sortie Service Bus dans un fichier *function.json* et une [fonction de script C#](functions-reference-csharp.md) qui utilise la liaison. La fonction utilise un déclencheur de minuteur pour envoyer un message de file d’attente toutes les 15 secondes.

Voici les données de liaison dans le fichier *function.json* :

```json
{
    "bindings": [
        {
            "schedule": "0/15 * * * * *",
            "name": "myTimer",
            "runsOnStartup": true,
            "type": "timerTrigger",
            "direction": "in"
        },
        {
            "name": "outputSbQueue",
            "type": "serviceBus",
            "queueName": "testqueue",
            "connection": "MyServiceBusConnection",
            "direction": "out"
        }
    ],
    "disabled": false
}
```

Voici le code de script C# qui crée un message unique :

```cs
public static void Run(TimerInfo myTimer, TraceWriter log, out string outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.Info(message); 
    outputSbQueue = message;
}
```

Voici le code de script C# qui crée plusieurs messages :

```cs
public static void Run(TimerInfo myTimer, TraceWriter log, ICollector<string> outputSbQueue)
{
    string message = $"Service Bus queue messages created at: {DateTime.Now}";
    log.Info(message); 
    outputSbQueue.Add("1 " + message);
    outputSbQueue.Add("2 " + message);
}
```

### <a name="output---f-example"></a>Sortie - exemple F#

L’exemple suivant montre une liaison de sortie Service Bus dans un fichier *function.json* et une [fonction de script F#](functions-reference-fsharp.md) qui utilise la liaison. La fonction utilise un déclencheur de minuteur pour envoyer un message de file d’attente toutes les 15 secondes.

Voici les données de liaison dans le fichier *function.json* :

```json
{
    "bindings": [
        {
            "schedule": "0/15 * * * * *",
            "name": "myTimer",
            "runsOnStartup": true,
            "type": "timerTrigger",
            "direction": "in"
        },
        {
            "name": "outputSbQueue",
            "type": "serviceBus",
            "queueName": "testqueue",
            "connection": "MyServiceBusConnection",
            "direction": "out"
        }
    ],
    "disabled": false
}
```

Voici le code de script F# qui crée un message unique :

```fsharp
let Run(myTimer: TimerInfo, log: TraceWriter, outputSbQueue: byref<string>) =
    let message = sprintf "Service Bus queue message created at: %s" (DateTime.Now.ToString())
    log.Info(message)
    outputSbQueue = message
```

### <a name="output---javascript-example"></a>Sortie - exemple JavaScript

L’exemple suivant montre une liaison de sortie Service Bus dans un fichier *function.json* et une [fonction JavaScript](functions-reference-node.md) qui utilise la liaison. La fonction utilise un déclencheur de minuteur pour envoyer un message de file d’attente toutes les 15 secondes.

Voici les données de liaison dans le fichier *function.json* :

```json
{
    "bindings": [
        {
            "schedule": "0/15 * * * * *",
            "name": "myTimer",
            "runsOnStartup": true,
            "type": "timerTrigger",
            "direction": "in"
        },
        {
            "name": "outputSbQueue",
            "type": "serviceBus",
            "queueName": "testqueue",
            "connection": "MyServiceBusConnection",
            "direction": "out"
        }
    ],
    "disabled": false
}
```

Voici le code de script JavaScript qui crée un message unique :

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueueMsg = message;
    context.done();
};
```

Voici le code de script JavaScript qui crée plusieurs messages :

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueueMsg = [];
    context.bindings.outputSbQueueMsg.push("1 " + message);
    context.bindings.outputSbQueueMsg.push("2 " + message);
    context.done();
};
```

## <a name="output---attributes"></a>Sortie - attributs

Dans les [bibliothèques de classes C#](functions-dotnet-class-library.md), utilisez l’attribut [ServiceBusAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAttribute.cs).

Le constructeur de l’attribut prend le nom de la file d’attente ou la rubrique et l’abonnement. Vous pouvez également spécifier les droits d’accès de la connexion. La détermination du paramètre des droits d’accès est expliquée dans la section [Sortie - configuration](#output---configuration). Voici un exemple qui illustre l’attribut appliqué à la valeur de retour de la fonction :

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue")]
public static string Run([HttpTrigger] dynamic input, TraceWriter log)
{
    ...
}
```

Vous pouvez définir la propriété `Connection` pour spécifier le compte Service Bus à utiliser, comme indiqué dans l’exemple suivant :

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string Run([HttpTrigger] dynamic input, TraceWriter log)
{
    ...
}
```

Pour obtenir un exemple complet, consultez [Sortie - exemple C#](#output---c-example).

Vous pouvez utiliser l’attribut `ServiceBusAccount` pour spécifier le compte Service Bus à utiliser au niveau de la classe, de la méthode ou du paramètre.  Pour plus d’informations, consultez [Déclencheur - attributs](#trigger---attributes).

## <a name="output---configuration"></a>Sortie - configuration

Le tableau suivant décrit les propriétés de configuration de liaison que vous définissez dans le fichier *function.json* et l’attribut `ServiceBus`.

|Propriété function.json | Propriété d’attribut |Description|
|---------|---------|----------------------|
|**type** | n/a | Doit être défini sur « serviceBus ». Cette propriété est définie automatiquement lorsque vous créez le déclencheur dans le portail Azure.|
|**direction** | n/a | Doit être défini sur « out ». Cette propriété est définie automatiquement lorsque vous créez le déclencheur dans le portail Azure. |
|**name** | n/a | Nom de la variable qui représente la file d’attente ou la rubrique dans le code de la fonction. Défini sur « $return » pour faire référence à la valeur de retour de la fonction. | 
|**queueName**|**QueueName**|Nom de la file d’attente.  Défini uniquement en cas d’envoi de messages de file d’attente, ne s’applique pas à une rubrique.
|**topicName**|**TopicName**|Nom de la rubrique à surveiller. Défini uniquement en cas d’envoi de messages de rubrique, ne s’applique pas à une file d’attente.|
|**Connexion**|**Connection**|Nom d’un paramètre d’application comportant la chaîne de connexion Service Bus à utiliser pour cette liaison. Si le nom du paramètre d’application commence par « AzureWebJobs », vous ne pouvez spécifier que le reste du nom. Par exemple, si vous définissez `connection` sur « MyServiceBus », le runtime Functions recherche un paramètre d’application qui est nommé « AzureWebJobsMyServiceBus ». Si vous laissez `connection` vide, le runtime Functions utilise la chaîne de connexion Service Bus par défaut dans le paramètre d’application nommé « AzureWebJobsServiceBus ».<br><br>Pour obtenir une chaîne de connexion, suivez les étapes indiquées à la section [Obtenir les informations d’identification de gestion](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md#obtain-the-management-credentials). La chaîne de connexion doit être destinée à un espace de noms Service Bus, et non limitée à une file d’attente ou une rubrique spécifique.|
|**accessRights**|**Access**|Droits d’accès de la chaîne de connexion. Les valeurs disponibles sont `manage` et `listen`. La valeur par défaut est `manage`, ce qui indique que `connection` a l'autorisation **Gérer**. Si vous utilisez une chaîne de connexion qui n’a pas l'autorisation **Gérer**, définissez `accessRights` sur « écouter ». Sinon, le runtime Functions pourrait échouer à effectuer des opérations qui nécessitent des droits de gestion. Dans Azure Functions version 2.x, cette propriété n’est pas disponible car la version la plus récente du SDK Stockage ne prend pas en charge les opérations de gestion.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Sortie - utilisation

Dans Azure Functions version 1.x, le runtime crée la file d’attente si elle n’existe pas et si vous avez défini `accessRights` sur `manage`. Dans Functions version 2.x, la file d’attente ou la rubrique doit déjà exister. Si vous spécifiez une file d’attente ou une rubrique qui n’existe pas, la fonction échoue. 

En langage C# et dans un script C#, vous pouvez utiliser les types de paramètres suivants pour la liaison de sortie :

* `out T paramName` - `T` peut être n’importe quel type sérialisable au format JSON. Si la valeur du paramètre est null lorsque la fonction se termine, Functions crée le message avec un objet null.
* `out string` - Si la valeur du paramètre est null lorsque la fonction se termine, Functions ne crée pas de message.
* `out byte[]` - Si la valeur du paramètre est null lorsque la fonction se termine, Functions ne crée pas de message.
* `out BrokeredMessage` - Si la valeur du paramètre est null lorsque la fonction se termine, Functions ne crée pas de message.
* `ICollector<T>` ou `IAsyncCollector<T>` - Pour la création de plusieurs messages. Un message est créé quand vous appelez la méthode `Add` .

Dans les fonctions asynchrones, utilisez la valeur de retour ou `IAsyncCollector` au lieu d’un paramètre `out`.

Dans JavaScript, accédez à la file d’attente ou la rubrique à l’aide de `context.bindings.<name from function.json>`. Vous pouvez affecter une chaîne, un tableau d’octets ou un objet Javascript (désérialisé dans JSON) à `context.binding.<name>`.

## <a name="exceptions-and-return-codes"></a>Exceptions et codes de retour

| Liaison | Informations de référence |
|---|---|
| Service Bus | [Codes d’erreur de Service Bus](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-messaging-exceptions) |
| Service Bus | [Limites de Service Bus](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-quotas) |

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [En savoir plus sur les déclencheurs et les liaisons Azure Functions](functions-triggers-bindings.md)
