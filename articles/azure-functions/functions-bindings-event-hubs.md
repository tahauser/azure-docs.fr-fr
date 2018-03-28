---
title: Liaisons Azure Event Hubs pour Azure Functions
description: Découvrez comment utiliser des liaisons Azure Event Hubs dans Azure Functions.
services: functions
documentationcenter: na
author: wesmc7777
manager: cfowler
editor: ''
tags: ''
keywords: azure functions, fonctions, traitement des événements, calcul dynamique, architecture sans serveur
ms.assetid: daf81798-7acc-419a-bc32-b5a41c6db56b
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/08/2017
ms.author: wesmc
ms.openlocfilehash: 87a7d25e1095fe1511c86dc56375c02f06f51b73
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="azure-event-hubs-bindings-for-azure-functions"></a>Liaisons Azure Event Hubs pour Azure Functions

Cet article explique comment utiliser des liaisons [Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) pour Azure Functions. Azure Functions prend en charge des liaisons de déclencheur et de sortie pour des Event Hubs.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages"></a>Packages

Les liaisons Event Hubs sont fournies dans le package NuGet [Microsoft.Azure.WebJobs.ServiceBus](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus). Le code source du package se trouve dans le référentiel GitHub [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/).

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="trigger"></a>Déclencheur

Utilisez le déclencheur Event Hubs pour répondre à un événement envoyé à un flux d’événements d’un hub d’événements. Vous devez disposer de l’accès en lecture au hub d’événements pour configurer le déclencheur.

Quand une fonction de déclenchement Event Hubs est déclenchée, le message qui le déclenche est passé à la fonction en tant que chaîne.

## <a name="trigger---scaling"></a>Déclencheur - mise à l'échelle

Chaque instance d'une fonction déclenchée par un hub d'événements est sauvegardée par 1 instance EventProcessorHost (EPH). Event Hubs garantit que 1 seule instance EPH peut obtenir un bail sur une partition donnée.

Par exemple, considérons la configuration et les hypothèses suivantes pour un hub d'événements :

1. 10 partitions.
1. 1 000 événements répartis uniformément sur toutes les partitions => 100 messages dans chaque partition.

Quand votre fonction est activée pour la première fois, il n'existe qu’une seule instance de la fonction. Appelons cette instance de fonction Function_0. Function_0 aura 1 EPH qui obtiendra un bail sur les 10 partitions. Elle commencera à lire les événements des partitions 0-9. À partir de ce point, l'un des événements suivants se produira :

* **1 seule instance de fonction est nécessaire** : Function_0 est capable de traiter l'ensemble des 1 000 événements avant que la logique de mise à l'échelle d'Azure Functions ne se déclenche. Par conséquent, tous les 1 000 messages sont traités par Function_0.

* **Ajouter 1 instance de fonction supplémentaire** : la logique de mise à l'échelle d'Azure Functions détermine que Function_0 reçoit plus de messages qu'elle ne peut traiter, et une nouvelle instance Function_1 est créée. Event Hubs détecte qu'une nouvelle instance EPH tente de lire des messages. Event Hubs démarre l'équilibrage de charge des partitions sur les instances EPH, par exemple, les partitions 0-4 sont affectées à Function_0 et les partitions 5 à 9 sont affectées à Function_1. 

* **Ajouter N autres instances de fonction** : la logique de mise à l'échelle d'Azure Functions détermine que Function_0 et Function_1 reçoivent plus de messages qu'elles ne peuvent traiter. Une nouvelle mise à l’échelle sera effectuée pour Function_2...N, où N est supérieur aux partitions du hub d’événements. Event Hubs équilibrera les partitions entre les instances Function_0...9.

La logique actuelle de mise à l'échelle d'Azure Functions est unique car N est supérieur au nombre de partitions. Cela permet de s'assurer qu'il y a toujours des instances EPH disponibles pour verrouiller rapidement les partitions à mesure qu'elles deviennent disponibles à partir d'autres instances. Les utilisateurs ne sont facturés que pour les ressources utilisées lors de l'exécution de l'instance de la fonction. Ils ne sont pas facturés pour ce surprovisionnement.

Si toutes les exécutions de fonction réussissent sans erreurs, des points de contrôle sont ajoutés au compte de stockage associé. Une fois les points de contrôle correctement créés, tous les 1 000 messages ne devraient plus jamais être récupérés.

## <a name="trigger---example"></a>Déclencheur - exemple

Consultez l’exemple propre à un langage particulier :

* [C#](#trigger---c-example)
* [Script C# (.csx)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [JavaScript](#trigger---javascript-example)

### <a name="trigger---c-example"></a>Déclencheur - exemple C#

L’exemple suivant illustre un code [de fonction C#](functions-dotnet-class-library.md) qui consigne le corps du message du déclencheur de hub d’événements.

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, TraceWriter log)
{
    log.Info($"C# Event Hub trigger function processed a message: {myEventHubMessage}");
}
```

Pour accéder aux métadonnées d’événement, effectuez une liaison avec l’objet [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) (nécessite une instruction `using` pour `Microsoft.ServiceBus.Messaging`).

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] EventData myEventHubMessage, TraceWriter log)
{
    log.Info($"{Encoding.UTF8.GetString(myEventHubMessage.GetBytes())}");
}
```
Pour recevoir des événements en lot, transformez `string` ou `EventData` en tableau :

```cs
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string[] eventHubMessages, TraceWriter log)
{
    foreach (var message in eventHubMessages)
    {
        log.Info($"C# Event Hub trigger function processed a message: {message}");
    }
}
```

### <a name="trigger---c-script-example"></a>Déclencheur - exemple Script C#

L’exemple suivant illustre une liaison de déclencheur de hub d’événements dans un fichier *function.json* et une [fonction de script C#](functions-reference-csharp.md) qui utilise la liaison. La fonction consigne le corps du message du déclencheur de hub d’événements.

Voici les données de liaison dans le fichier *function.json* :

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```
Voici le code Script C# :

```cs
using System;

public static void Run(string myEventHubMessage, TraceWriter log)
{
    log.Info($"C# Event Hub trigger function processed a message: {myEventHubMessage}");
}
```

Pour accéder aux métadonnées d’événement, effectuez une liaison avec l’objet [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) (nécessite une instruction using pour `Microsoft.ServiceBus.Messaging`).

```cs
#r "Microsoft.ServiceBus"
using System.Text;
using Microsoft.ServiceBus.Messaging;

public static void Run(EventData myEventHubMessage, TraceWriter log)
{
    log.Info($"{Encoding.UTF8.GetString(myEventHubMessage.GetBytes())}");
}
```

Pour recevoir des événements en lot, transformez `string` ou `EventData` en tableau :

```cs
public static void Run(string[] eventHubMessages, TraceWriter log)
{
    foreach (var message in eventHubMessages)
    {
        log.Info($"C# Event Hub trigger function processed a message: {message}");
    }
}
```

### <a name="trigger---f-example"></a>Déclencheur - exemple F#

L’exemple suivant illustre une liaison de déclencheur Event Hub dans un fichier *function.json* et une [fonction F#](functions-reference-fsharp.md) qui utilise la liaison. La fonction consigne le corps du message du déclencheur de hub d’événements.

Voici les données de liaison dans le fichier *function.json* :

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Voici le code F# :

```fsharp
let Run(myEventHubMessage: string, log: TraceWriter) =
    log.Info(sprintf "F# eventhub trigger function processed work item: %s" myEventHubMessage)
```

### <a name="trigger---javascript-example"></a>Déclencheur - exemple JavaScript

L’exemple suivant illustre une liaison de déclencheur Event Hub dans un fichier *function.json* et une [fonction JavaScript](functions-reference-node.md) qui utilise la liaison. La fonction consigne le corps du message du déclencheur de hub d’événements.

Voici les données de liaison dans le fichier *function.json* :

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Voici le code JavaScript :

```javascript
module.exports = function (context, myEventHubMessage) {
    context.log('Node.js eventhub trigger function processed work item', myEventHubMessage);    
    context.done();
};
```

## <a name="trigger---attributes"></a>Déclencheur - attributs

Dans les [bibliothèques de classes C#](functions-dotnet-class-library.md), utilisez l’attribut [EventHubTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubTriggerAttribute.cs).

Le constructeur de l’attribut prend le nom du hub d’événements, le nom du groupe de consommateurs et le nom d’un paramètre d’application qui contient la chaîne de connexion. Pour plus d’informations sur ces paramètres, consultez la [section de configuration du déclencheur](#trigger---configuration). Voici un exemple d’attribut `EventHubTriggerAttribute` :

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, TraceWriter log)
{
    ...
}
```

Pour obtenir un exemple complet, consultez [Déclencheur - exemple C#](#trigger---c-example).

## <a name="trigger---configuration"></a>Déclencheur - configuration

Le tableau suivant décrit les propriétés de configuration de liaison que vous définissez dans le fichier *function.json* et l’attribut `EventHubTrigger`.

|Propriété function.json | Propriété d’attribut |Description|
|---------|---------|----------------------|
|**type** | n/a | Cette propriété doit être définie sur `eventHubTrigger`. Cette propriété est définie automatiquement lorsque vous créez le déclencheur dans le portail Azure.|
|**direction** | n/a | Cette propriété doit être définie sur `in`. Cette propriété est définie automatiquement lorsque vous créez le déclencheur dans le portail Azure. |
|**name** | n/a | Nom de la variable qui représente l’élément d’événement dans le code de la fonction. | 
|**path** |**EventHubName** | Nom du hub d’événements. | 
|**consumerGroup** |**ConsumerGroup** | Propriété facultative qui définit le [groupe de consommateurs](../event-hubs/event-hubs-features.md#event-consumers) utilisé pour l’abonnement à des événements dans le hub. En cas d’omission, le groupe de consommateurs `$Default` est utilisé. | 
|**Connexion** |**Connection** | Le nom d’un paramètre d’application qui contient la chaîne de connexion à l’espace de noms du hub d’événements. Copiez cette chaîne de connexion en cliquant sur le bouton **Informations de connexion** pour [l’espace de noms](../event-hubs/event-hubs-create.md#create-an-event-hubs-namespace), et non pour le hub d’événements lui-même. Cette chaîne de connexion doit avoir au moins des droits de lecture pour activer le déclencheur.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---hostjson-properties"></a>Déclencheur - propriétés de host.json

Le fichier [host.json](functions-host-json.md#eventhub) contient les paramètres qui contrôlent le comportement du déclencheur Event Hubs.

[!INCLUDE [functions-host-json-event-hubs](../../includes/functions-host-json-event-hubs.md)]

## <a name="output"></a>Sortie

Utilisez la liaison de sortie Event Hubs pour écrire des événements dans un flux d’événements du hub d’événements. Vous devez disposer de l’autorisation d’envoi à un hub d’événements pour y écrire les événements.

## <a name="output---example"></a>Sortie - exemple

Consultez l’exemple propre à un langage particulier :

* [C#](#output---c-example)
* [Script C# (.csx)](#output---c-script-example)
* [F#](#output---f-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>Sortie - exemple C#

L’exemple suivant illustre une [fonction C#](functions-dotnet-class-library.md) qui écrit un message dans un hub d’événements, en utilisant la valeur retournée par la méthode comme sortie :

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, TraceWriter log)
{
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
    return $"{DateTime.Now}";
}
```

### <a name="output---c-script-example"></a>Sortie - exemple Script C#

L’exemple suivant illustre une liaison de déclencheur de hub d’événements dans un fichier *function.json* et une [fonction de script C#](functions-reference-csharp.md) qui utilise la liaison. La fonction écrit un message dans un hub d’événements.

Voici les données de liaison dans le fichier *function.json* :

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

Voici le code Script C# qui crée un message :

```cs
using System;

public static void Run(TimerInfo myTimer, out string outputEventHubMessage, TraceWriter log)
{
    String msg = $"TimerTriggerCSharp1 executed at: {DateTime.Now}";
    log.Verbose(msg);   
    outputEventHubMessage = msg;
}
```

Voici le code de script C# qui crée plusieurs messages :

```cs
public static void Run(TimerInfo myTimer, ICollector<string> outputEventHubMessage, TraceWriter log)
{
    string message = $"Event Hub message created at: {DateTime.Now}";
    log.Info(message);
    outputEventHubMessage.Add("1 " + message);
    outputEventHubMessage.Add("2 " + message);
}
```

### <a name="output---f-example"></a>Sortie - exemple F#

L’exemple suivant illustre une liaison de déclencheur Event Hub dans un fichier *function.json* et une [fonction F#](functions-reference-fsharp.md) qui utilise la liaison. La fonction écrit un message dans un hub d’événements.

Voici les données de liaison dans le fichier *function.json* :

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

Voici le code F# :

```fsharp
let Run(myTimer: TimerInfo, outputEventHubMessage: byref<string>, log: TraceWriter) =
    let msg = sprintf "TimerTriggerFSharp1 executed at: %s" DateTime.Now.ToString()
    log.Verbose(msg);
    outputEventHubMessage <- msg;
```

### <a name="output---javascript-example"></a>Sortie - exemple JavaScript

L’exemple suivant illustre une liaison de déclencheur Event Hub dans un fichier *function.json* et une [fonction JavaScript](functions-reference-node.md) qui utilise la liaison. La fonction écrit un message dans un hub d’événements.

Voici les données de liaison dans le fichier *function.json* :

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

Voici le code JavaScript qui envoie un message unique :

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();
    context.log('Event Hub message created at: ', timeStamp);   
    context.bindings.outputEventHubMessage = "Event Hub message created at: " + timeStamp;
    context.done();
};
```

Voici le code JavaScript qui envoie plusieurs messages :

```javascript
module.exports = function(context) {
    var timeStamp = new Date().toISOString();
    var message = 'Event Hub message created at: ' + timeStamp;

    context.bindings.outputEventHubMessage = [];

    context.bindings.outputEventHubMessage.push("1 " + message);
    context.bindings.outputEventHubMessage.push("2 " + message);
    context.done();
};
```

## <a name="output---attributes"></a>Sortie - attributs

Dans les [bibliothèques de classes C#](functions-dotnet-class-library.md), utilisez l’attribut [EventHubAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs).

Le constructeur de l’attribut prend le nom du hub d’événements, le hub d’événements et le nom d’un paramètre d’application qui contient la chaîne de connexion. Pour plus d’informations sur ces paramètres, consultez [Sortie - configuration](#output---configuration). Voici un exemple d’attribut `EventHub` :

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, TraceWriter log)
{
    ...
}
```

Pour obtenir un exemple complet, consultez [Sortie - exemple C#](#output---c-example).

## <a name="output---configuration"></a>Sortie - configuration

Le tableau suivant décrit les propriétés de configuration de liaison que vous définissez dans le fichier *function.json* et l’attribut `EventHub`.

|Propriété function.json | Propriété d’attribut |Description|
|---------|---------|----------------------|
|**type** | n/a | Doit être défini sur eventHub. |
|**direction** | n/a | Doit être défini sur « out ». Ce paramètre est défini automatiquement lorsque vous créez la liaison dans le portail Azure. |
|**name** | n/a | Nom de variable utilisé dans le code de la fonction qui représente l’événement. | 
|**path** |**EventHubName** | Nom du hub d’événements. | 
|**Connexion** |**Connection** | Le nom d’un paramètre d’application qui contient la chaîne de connexion à l’espace de noms du hub d’événements. Copiez cette chaîne de connexion en cliquant sur le bouton **Informations de connexion** pour *l’espace de noms*, et non pour le hub d’événements lui-même. Cette chaîne de connexion doit disposer d’autorisations d’envoi pour envoyer le message au flux d’événements.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Sortie - utilisation

Dans C# et Script C#, envoyez des messages en utilisant un paramètre de méthode comme `out string paramName`. Dans Script C#, `paramName` est la valeur spécifiée dans la propriété `name` de *function.json*. Pour écrire plusieurs messages, vous pouvez utiliser `ICollector<string>` ou `IAsyncCollector<string>` à la place de `out string`.

Dans JavaScript, accédez à l’événement de sortie à l’aide de `context.bindings.<name>`. `<name>` est la valeur spécifiée dans la propriété `name` de *function.json*.

## <a name="exceptions-and-return-codes"></a>Exceptions et codes de retour

| Liaison | Informations de référence |
|---|---|
| Event Hub | [Guide des opérations](https://docs.microsoft.com/rest/api/eventhub/publisher-policy-operations) |

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [En savoir plus sur les déclencheurs et les liaisons Azure Functions](functions-triggers-bindings.md)
