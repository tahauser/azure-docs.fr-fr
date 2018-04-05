---
title: Déclencheur de minuteur pour Azure Functions
description: Découvrez comment utiliser des déclencheurs de minuteur dans Azure Functions.
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: ''
tags: ''
keywords: azure functions, fonctions, traitement des événements, calcul dynamique, architecture sans serveur
ms.assetid: d2f013d1-f458-42ae-baf8-1810138118ac
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/27/2017
ms.author: tdykstra
ms.custom: ''
ms.openlocfilehash: 89469af2b1d02ef00fc347e47719956885e7f142
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="timer-trigger-for-azure-functions"></a>Déclencheur de minuteur pour Azure Functions 

Cet article explique comment utiliser des déclencheurs de minuteur dans Azure Functions. Un déclencheur de minuteur vous permet d’exécuter une fonction de manière planifiée. 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages"></a>Packages

Le déclencheur de minuteur est fourni dans le package NuGet [Microsoft.Azure.WebJobs.Extensions](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions). Le code source du package se trouve dans le référentiel GitHub [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/).

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="example"></a>Exemples

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
    if(myTimer.IsPastDue)
    {
        log.Info("Timer is running late!");
    }
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

Dans les [bibliothèques de classes C#](functions-dotnet-class-library.md), utilisez l’attribut [TimerTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerTriggerAttribute.cs).

Le constructeur de l’attribut prend une expression CRON ou `TimeSpan`. Vous pouvez utiliser `TimeSpan` uniquement si l’application de fonction s’exécute sur un plan App Service. L’exemple suivant illustre une expression CRON :

```csharp
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, TraceWriter log)
{
    if (myTimer.IsPastDue)
    {
        log.Info("Timer is running late!");
    }
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
}
 ```

## <a name="configuration"></a>Configuration

Le tableau suivant décrit les propriétés de configuration de liaison que vous définissez dans le fichier *function.json* et l’attribut `TimerTrigger`.

|Propriété function.json | Propriété d’attribut |Description|
|---------|---------|----------------------|
|**type** | n/a | Doit avoir la valeur « timerTrigger ». Cette propriété est définie automatiquement lorsque vous créez le déclencheur dans le portail Azure.|
|**direction** | n/a | Doit être défini sur « in ». Cette propriété est définie automatiquement lorsque vous créez le déclencheur dans le portail Azure. |
|**name** | n/a | Nom de la variable qui représente l’objet de minuteur dans le code de la fonction. | 
|**schedule**|**ScheduleExpression**|Une [expression CRON](#cron-expressions) ou une valeur [TimeSpan](#timespan). `TimeSpan` peut être utilisé uniquement pour une application de fonction qui s’exécute sur un plan App Service. Vous pouvez placer l’expression de planification dans un paramètre d’application et affecter à cette propriété le nom du paramètre d’application encapsulé dans des signes **%**, comme dans cet exemple : « %nom_de_paramètre_d’application_avec_expression_planification% ». |
|**runOnStartup**|**RunOnStartup**|Si la valeur est `true`, la fonction est appelée au démarrage du runtime. Par exemple, le runtime démarre lorsque l’application de fonction sort de veille après une période d’inactivité. Lorsque l’application de fonction redémarre en raison de modifications apportées à la fonction, et lorsque l’application de fonction augmente la taille des instances. Par conséquent, **runOnStartup** devrait rarement voire ne jamais être défini sur la valeur `true`, cela provoquerait l’exécution du code à des moments hautement imprévisibles. Si vous avez besoin de déclencher la fonction en dehors de la planification du minuteur, vous pouvez créer une deuxième fonction avec un type de déclencheur différent et partager le code entre les deux fonctions. Par exemple, pour déclencher la fonction au moment du déploiement, vous pouvez [personnaliser votre déploiement](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) pour que la deuxième fonction soit appelée via l’exécution d’une requête HTTP à la fin du déploiement.|
|**useMonitor**|**UseMonitor**|Peut-être défini sur la valeur `true` ou `false` pour indiquer si la planification doit être surveillée ou non. La surveillance de planification conserve les occurrences de planification pour garantir la maintenance correcte de cette dernière même en cas de redémarrage des instances de l’application de fonction. Si elle n’est pas définie explicitement, la valeur par défaut est `true` pour les planifications dont l’intervalle de récurrence est supérieur à 1 minute. Pour les planifications qui se déclenchent plusieurs fois par minute, la valeur par défaut est `false`.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Usage

Lorsqu’une fonction de déclenchement du minuteur est appelée, [l’objet minuteur](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs) est transmis à la fonction. Le code JSON suivant est un exemple de représentation de l’objet minuteur. 

```json
{
    "Schedule":{
    },
    "ScheduleStatus": {
        "Last":"2016-10-04T10:15:00+00:00",
        "LastUpdated":"2016-10-04T10:16:00+00:00",
        "Next":"2016-10-04T10:20:00+00:00"
    },
    "IsPastDue":false
}
```

La propriété `IsPastDue` est `true` lorsque l’appel de fonction en cours arrive plus tard que prévu. Par exemple, un redémarrage de la fonction d’application peut entraîner l’échec d’un appel.

## <a name="cron-expressions"></a>Expressions CRON 

Une expression CRON pour le déclencheur de minuteur Azure Functions comprend six champs : 

`{second} {minute} {hour} {day} {month} {day-of-week}`

Chaque champ peut être associé aux types de valeurs suivants :

|type  |Exemples  |En cas de déclenchement  |
|---------|---------|---------|
|Une valeur spécifique |<nobr>"0 5 * * * *"</nobr>|à hh:05:00 où hh correspond à toutes les heures (une fois par heure)|
|Toutes les valeurs (`*`)|<nobr>"0 * 5 * * *"</nobr>|à 5:mm:00 chaque jour, où mm correspond à toutes les minutes de l’heure (60 fois par jour)|
|Une plage (opérateur `-`)|<nobr>"5-7 * * * * *"</nobr>|à hh:mm:05, hh:mm:06 et hh:mm:07 où hh:mm correspond à toutes les minutes de toutes les heures (3 fois par minute)|  
|Un ensemble de valeurs (opérateur `,`)|<nobr>"5,8,10 * * * * *"</nobr>|à hh:mm:05, hh:mm:08 et hh:mm:10 où hh:mm correspond à toutes les minutes de toutes les heures (3 fois par minute)|
|Une valeur d’intervalle (opérateur `/`)|<nobr>"0 */5 * * * *"</nobr>|à hh:05:00, hh:10:00, hh:15:00 et ainsi de suite jusqu’à hh:55:00, où hh correspond à toutes les heures (12 fois par heure)|

### <a name="cron-examples"></a>Exemples CRON

Voici quelques exemples d’expressions CRON que vous pouvez utiliser pour le déclencheur de minuteur dans Azure Functions.

|Exemples|En cas de déclenchement  |
|---------|---------|
|"0 */5 * * * *"|une fois toutes les cinq minutes|
|"0 0 * * * *"|une fois toutes les heures|
|"0 0 */2 * * *"|une fois toutes les deux heures|
|"0 0 9-17 * * *"|une fois toutes les heures entre 9h et 17h|
|"0 30 9 * * *"|à 9h30 tous les jours|
|"0 30 9 * * 1-5"|à 9h30 tous les jours de la semaine|

>[!NOTE]   
>Vous trouverez des exemples d’expressions CRON en ligne, mais nombre d’entre elles omettent le champ `{second}`. Si vous copiez à partir de l’une d’elles, ajoutez le champ `{second}` manquant. Il est généralement plus judicieux de le renseigner avec un zéro plutôt qu’un astérisque.

### <a name="cron-time-zones"></a>Fuseaux horaires CRON

Les nombres d’une expression CRON font référence à une heure et à une date, pas à un intervalle de temps. Par exemple, un 5 dans le champ `hour` correspond à 17h, pas à toutes les 5 heures.

Le fuseau horaire par défaut utilisé avec les expressions CRON est le Temps universel coordonné (UTC). Pour baser votre expression CRON sur un autre fuseau horaire, créez un paramètre d’application nommé `WEBSITE_TIME_ZONE` pour votre application de fonction. Définissez la valeur sur le nom du fuseau horaire souhaité comme indiqué dans l’[index des fuseaux horaires de Microsoft](https://technet.microsoft.com/library/cc749073). 

Par exemple, *l’heure de l’Est* correspond à UTC-05:00. Pour que votre déclencheur de minuteur se déclenche chaque jour à 10 h 00 (heure de l’Est), vous pouvez utiliser l’expression CRON suivante, qui tient compte du fuseau horaire UTC :

```json
"schedule": "0 0 15 * * *",
``` 

Sinon, vous pouvez créer un paramètre d’application pour votre application de fonction nommé `WEBSITE_TIME_ZONE` et définir la valeur sur **Est**.  Utilisez ensuite l’expression CRON suivante : 

```json
"schedule": "0 0 10 * * *",
``` 

## <a name="timespan"></a>intervalle de temps

 `TimeSpan` peut être utilisé uniquement pour une application de fonction qui s’exécute sur un plan App Service.

Contrairement à une expression CRON, une valeur `TimeSpan` spécifie l’intervalle de temps entre chaque appel de fonction. Quand une fonction se termine après une exécution plus longue que l’intervalle spécifié, le minuteur rappelle immédiatement la fonction.

Exprimé sous forme de chaîne, le format `TimeSpan` est `hh:mm:ss` lorsque la valeur de `hh` est inférieure à 24. Lorsque les deux premiers chiffres sont 24 ou plus, le format est `dd:hh:mm`. Voici quelques exemples :

|Exemples |En cas de déclenchement  |
|---------|---------|
|"01:00:00" | toutes les heures        |
|"00:01:00"|chaque minute         |
|"24:00:00" | tous les 24 jours        |

## <a name="scale-out"></a>Montée en charge

Si une application de fonction augmente la taille de plusieurs instances, une seule instance de fonction déclenchée par minuteur est exécutée sur toutes les instances.

## <a name="function-apps-sharing-storage"></a>Applications de fonction qui partagent du stockage

Si vous partagez un compte de stockage entre plusieurs applications de fonction, assurez-vous que chacune d’entre elles a une `id` différente dans *host.json*. Vous pouvez omettre la propriété `id` ou définir manuellement chaque `id` de l’application de fonction sur une autre valeur. Le déclencheur du minuteur utilise un verrou de stockage pour vous assurer qu’il n’y aura qu’une seule instance du minuteur lorsqu’une application de fonction augmentera la taille de plusieurs instances. Si deux applications de fonction partagent le même `id` et que chacune utilise un déclencheur de minuteur, un seul minuteur s’exécutera.

## <a name="retry-behavior"></a>Comportement pour les nouvelles tentatives

À la différence du déclencheur de file d’attente, le déclencheur de minuteur n’effectue pas de nouvelle tentative après l’échec d’une fonction. En cas d’échec d’une fonction, elle n’est pas rappelée avant la prochaine période planifiée.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Accéder à un guide de démarrage rapide qui utilise un déclencheur de minuteur](functions-create-scheduled-function.md)

> [!div class="nextstepaction"]
> [En savoir plus sur les déclencheurs et les liaisons Azure Functions](functions-triggers-bindings.md)
