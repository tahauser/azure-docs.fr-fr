---
title: Liaisons SendGrid dans Azure Functions
description: "Informations de référence sur les liaisons SendGrid dans Azure Functions."
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/29/2017
ms.author: tdykstra
ms.openlocfilehash: aa64bbc764fe96d7c15d62660114222458e5acf4
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/02/2018
---
# <a name="azure-functions-sendgrid-bindings"></a>Liaisons SendGrid dans Azure Functions

Cet article explique comment envoyer des e-mails avec des liaisons [SendGrid](https://sendgrid.com/docs/User_Guide/index.html) dans Azure Functions. Azure Functions prend en charge une liaison de sortie pour SendGrid.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="example"></a>exemples

Consultez l’exemple propre à un langage particulier :

* [C#](#c-example)
* [Script C# (.csx)](#c-script-example)
* [JavaScript](#javascript-example)

### <a name="c-example"></a>Exemple en code C#

L’exemple suivant montre une [fonction C#](functions-dotnet-class-library.md) qui utilise un déclencheur de file d’attente Service Bus et une liaison de sortie SendGrid.

```cs
[FunctionName("SendEmail")]
public static void Run(
    [ServiceBusTrigger("myqueue", AccessRights.Manage, Connection = "ServiceBusConnection")] OutgoingEmail email,
    [SendGrid(ApiKey = "CustomSendGridKeyAppSettingName")] out SendGridMessage message)
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

Vous pouvez omettre la définition de la propriété `ApiKey` de l’attribut si votre clé API se trouve dans un paramètre d’application nommé « AzureWebJobsSendGridApiKey ».

### <a name="c-script-example"></a>Exemple de script C#

L’exemple suivant montre une liaison de sortie SendGrid dans un fichier *function.json* et une [fonction de script C#](functions-reference-csharp.md) qui utilise la liaison.

Voici les données de liaison dans le fichier *function.json* :

```json 
{
    "bindings": [
        {
            "name": "message",
            "type": "sendGrid",
            "direction": "out",
            "apiKey" : "MySendGridKey",
            "to": "{ToEmail}",
            "from": "{FromEmail}",
            "subject": "SendGrid output bindings"
        }
    ]
}
```

La section [configuration](#configuration) décrit ces propriétés.

Voici le code Script C# :

```csharp
#r "SendGrid"
using System;
using SendGrid.Helpers.Mail;

public static void Run(TraceWriter log, string input, out Mail message)
{
     message = new Mail
    {        
        Subject = "Azure news"          
    };

    var personalization = new Personalization();
    personalization.AddTo(new Email("recipient@contoso.com"));   

    Content content = new Content
    {
        Type = "text/plain",
        Value = input
    };
    message.AddContent(content);
    message.AddPersonalization(personalization);
}
```

### <a name="javascript-example"></a>Exemple JavaScript

L’exemple suivant montre une liaison de sortie SendGrid dans un fichier *function.json* et une [fonction JavaScript](functions-reference-node.md) qui utilise la liaison.

Voici les données de liaison dans le fichier *function.json* :

```json 
{
    "bindings": [
        {
            "name": "$return",
            "type": "sendGrid",
            "direction": "out",
            "apiKey" : "MySendGridKey",
            "to": "{ToEmail}",
            "from": "{FromEmail}",
            "subject": "SendGrid output bindings"
        }
    ]
}
```

La section [configuration](#configuration) décrit ces propriétés.

Voici le code JavaScript :

```javascript
module.exports = function (context, input) {    
    var message = {
         "personalizations": [ { "to": [ { "email": "sample@sample.com" } ] } ],
        from: { email: "sender@contoso.com" },        
        subject: "Azure news",
        content: [{
            type: 'text/plain',
            value: input
        }]
    };

    context.done(null, message);
};
```

## <a name="attributes"></a>Attributs

Dans les [bibliothèques de classes C#](functions-dotnet-class-library.md), utilisez l’attribut [SendGrid](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.SendGrid/SendGridAttribute.cs), défini dans le package NuGet [Microsoft.Azure.WebJobs.Extensions.SendGrid](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SendGrid).

Pour plus d’informations sur les propriétés d’attribut que vous pouvez configurer, consultez [Configuration](#configuration). Voici un exemple d’attribut `SendGrid` dans une signature de méthode :

```csharp
[FunctionName("SendEmail")]
public static void Run(
    [ServiceBusTrigger("myqueue", AccessRights.Manage, Connection = "ServiceBusConnection")] OutgoingEmail email,
    [SendGrid(ApiKey = "CustomSendGridKeyAppSettingName")] out SendGridMessage message)
{
    ...
}
```

Vous trouverez un exemple complet sur la page [Exemple C#](#c-example).

## <a name="configuration"></a>Configuration

Le tableau suivant décrit les propriétés de configuration de liaison que vous définissez dans le fichier *function.json* et l’attribut `SendGrid`.

|Propriété function.json | Propriété d’attribut |Description|
|---------|---------|----------------------|
|**type**|| Obligatoire : doit être défini sur `sendGrid`.|
|**direction**|| Obligatoire : doit être défini sur `out`.|
|**name**|| Obligatoire : nom de variable utilisé dans le code de la fonction pour la requête ou le corps de la requête. Cette valeur est ```$return``` lorsqu’il n’existe qu’une valeur de retour. |
|**apiKey**|**ApiKey**| Nom d’un paramètre d’application qui contient votre clé API. En l’absence de définition, le nom du paramètre d’application par défaut est « AzureWebJobsSendGridApiKey ».|
|**to**|**To**| Adresse e-mail du destinataire. |
|**from**|**From**| Adresse e-mail de l’expéditeur. |
|**subject**|**Objet**| Objet de l’e-mail. |
|**text**|**Text**| Contenu de l’e-mail. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="next-steps"></a>étapes suivantes

> [!div class="nextstepaction"]
> [En savoir plus sur les déclencheurs et les liaisons Azure Functions](functions-triggers-bindings.md)