---
title: "Déclencheur de minuteur pour Azure Functions"
description: "Découvrez comment utiliser des déclencheurs de minuteur dans Azure Functions."
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: 
tags: 
keywords: "azure functions, fonctions, traitement des événements, calcul dynamique, architecture sans serveur"
ms.assetid: d2f013d1-f458-42ae-baf8-1810138118ac
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/27/2017
ms.author: tdykstra
ms.custom: 
ms.openlocfilehash: eeb8833470b2ba003ba74b1db57bbd2bbbb7f65d
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/02/2018
---
# <a name="timer-trigger-for-azure-functions"></a>Déclencheur de minuteur pour Azure Functions 

Cet article explique comment utiliser des déclencheurs de minuteur dans Azure Functions. Un déclencheur de minuteur vous permet d’exécuter une fonction de manière planifiée. 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="example"></a>Exemple

Consultez l’exemple propre à un langage particulier :

* [C#](#trigger---c-example)
* [Script C# (.csx)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [JavaScript](#trigger---javascript-example)

### <a name="c-example"></a>Exemple en code C#

L’exemple suivant montre une [fonction C#](functions-dotnet-class-library.md) qui s’exécute toutes les cinq minutes :

```cs
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, TraceWriter log)
{
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
}
```

### <a name="c-script-example"></a>Exemple de script C#

L’exemple suivant montre une liaison de déclencheur de minuteur dans un fichier *function.json* et une [fonction de script C#](functions-reference-csharp.md) qui utilise la liaison. La fonction écrit un journal indiquant si cet appel de fonction est dû à une occurrence de planification manquée.

Voici les données de liaison dans le fichier *function.json* :

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

Voici le code Script C# :

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log)
{
    if(myTimer.IsPastDue)
    {
        log.Info("Timer is running late!");
    }
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}" );  
}
```

### <a name="f-example"></a>Exemple F#

L’exemple suivant montre une liaison de déclencheur de minuteur dans un fichier *function.json* et une [fonction de script F#](functions-reference-fsharp.md) qui utilise la liaison. La fonction écrit un journal indiquant si cet appel de fonction est dû à une occurrence de planification manquée.

Voici les données de liaison dans le fichier *function.json* :

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

Voici le code de script F# :

```fsharp
let Run(myTimer: TimerInfo, log: TraceWriter ) =
    if (myTimer.IsPastDue) then
        log.Info("F# function is running late.")
    let now = DateTime.Now.ToLongTimeString()
    log.Info(sprintf "F# function executed at %s!" now)
```

### <a name="javascript-example"></a>Exemple JavaScript

L’exemple suivant montre une liaison de déclencheur de minuteur dans un fichier *function.json* et une [fonction JavaScript](functions-reference-node.md) qui utilise la liaison. La fonction écrit un journal indiquant si cet appel de fonction est dû à une occurrence de planification manquée.

Voici les données de liaison dans le fichier *function.json* :

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

Voici le code de script JavaScript :

```JavaScript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    if(myTimer.isPastDue)
    {
        context.log('Node.js is running late!');
    }
    context.log('Node.js timer trigger function ran!', timeStamp);   

    context.done();
};
```

## <a name="attributes"></a>Attributs

Dans les [bibliothèques de classes C#](functions-dotnet-class-library.md), utilisez l’attribut [TimerTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerTriggerAttribute.cs), défini dans le package NuGet [Microsoft.Azure.WebJobs.Extensions](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions).

Le constructeur de l’attribut prend une expression CRON, comme illustré dans l’exemple suivant :

```csharp
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, TraceWriter log)
{
   ...
}
 ```

Vous pouvez spécifier un `TimeSpan` au lieu d’une expression CRON si votre application de fonction s’exécute sur un plan App Service (et non un plan Consommation).

Vous trouverez un exemple complet sur la page [Exemple C#](#c-example).

## <a name="configuration"></a>Configuration

Le tableau suivant décrit les propriétés de configuration de liaison que vous définissez dans le fichier *function.json* et l’attribut `TimerTrigger`.

|Propriété function.json | Propriété d’attribut |Description|
|---------|---------|----------------------|
|**type** | n/a | Doit avoir la valeur « timerTrigger ». Cette propriété est définie automatiquement lorsque vous créez le déclencheur dans le portail Azure.|
|**direction** | n/a | Doit être défini sur « in ». Cette propriété est définie automatiquement lorsque vous créez le déclencheur dans le portail Azure. |
|**name** | n/a | Nom de la variable qui représente l’objet de minuteur dans le code de la fonction. | 
|**schedule**|**ScheduleExpression**|Sur le plan Consommation, vous pouvez définir des planifications avec une expression CRON. Si vous utilisez un plan App Service, vous pouvez également utiliser une chaîne `TimeSpan`. Les sections suivantes expliquent les expressions CRON. Vous pouvez placer l’expression de planification dans un paramètre d’application et affecter à cette propriété une valeur encapsulée dans des signes **%**, comme dans cet exemple : « %nom_de_paramètre_d’application_avec_expression_CRON% ». |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

### <a name="cron-format"></a>Format CRON 

Une [expression CRON](http://en.wikipedia.org/wiki/Cron#CRON_expression) pour le déclencheur de minuteur Azure Functions comprend les six champs suivants : 

```
{second} {minute} {hour} {day} {month} {day-of-week}
```

>[!NOTE]   
>De nombreuses expressions CRON disponibles en ligne omettent le champ `{second}`. Si vous copiez à partir de l’une d’elles, ajoutez le champ `{second}` manquant.

### <a name="cron-time-zones"></a>Fuseaux horaires CRON

Le fuseau horaire par défaut utilisé avec les expressions CRON est le Temps universel coordonné (UTC). Pour baser votre expression CRON sur un autre fuseau horaire, créez un nouveau paramètre d’application nommé `WEBSITE_TIME_ZONE` pour votre application de fonction. Définissez la valeur sur le nom du fuseau horaire souhaité comme indiqué dans l’[index des fuseaux horaires de Microsoft](https://technet.microsoft.com/library/cc749073(v=ws.10).aspx). 

Par exemple, *l’heure de l’Est* correspond à UTC-05:00. Pour que votre déclencheur de minuteur se déclenche chaque jour à 10 h 00 (heure de l’Est), vous pouvez utiliser l’expression CRON suivante, qui tient compte du fuseau horaire UTC :

```json
"schedule": "0 0 15 * * *",
``` 

Sinon, vous pouvez ajouter un nouveau paramètre d’application pour votre application de fonction nommé `WEBSITE_TIME_ZONE` et définir la valeur sur **Est**.  L’expression CRON suivante peut alors être utilisée pour 10h00 EST : 

```json
"schedule": "0 0 10 * * *",
``` 
### <a name="cron-examples"></a>Exemples CRON

Voici quelques exemples d’expressions CRON que vous pouvez utiliser pour le déclencheur de minuteur dans Azure Functions. 

Pour déclencher la fonction toutes les cinq minutes :

```json
"schedule": "0 */5 * * * *"
```

Pour déclencher la fonction toutes les heures :

```json
"schedule": "0 0 * * * *",
```

Pour déclencher la fonction toutes les deux heures:

```json
"schedule": "0 0 */2 * * *",
```

Pour déclencher la fonction toutes les heures entre 9h et 17h :

```json
"schedule": "0 0 9-17 * * *",
```

Pour déclencher la fonction à 9h30 tous les jours :

```json
"schedule": "0 30 9 * * *",
```

Pour déclencher la fonction à 9h30 tous les jours de la semaine :

```json
"schedule": "0 30 9 * * 1-5",
```

## <a name="usage"></a>Usage

Lorsqu’une fonction de déclenchement du minuteur est appelée, [l’objet minuteur](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs) est transmis à la fonction. Le code JSON suivant est un exemple de représentation de l’objet minuteur. 

```json
{
    "Schedule":{
    },
    "ScheduleStatus": {
        "Last":"2016-10-04T10:15:00.012699+00:00",
        "Next":"2016-10-04T10:20:00+00:00"
    },
    "IsPastDue":false
}
```

## <a name="scale-out"></a>Montée en charge

Le déclencheur du minuteur prend en charge la montée en charge multi-instance. Une instance unique d’une fonction de minuteur spécifique est exécutée sur toutes les instances.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Accéder à un guide de démarrage rapide qui utilise un déclencheur de minuteur](functions-create-scheduled-function.md)

> [!div class="nextstepaction"]
> [En savoir plus sur les déclencheurs et les liaisons Azure Functions](functions-triggers-bindings.md)
