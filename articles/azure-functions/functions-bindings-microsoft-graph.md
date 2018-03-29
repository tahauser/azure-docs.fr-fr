---
title: Liaisons Microsoft Graph pour Azure Functions
description: Découvrez comment utiliser des déclencheurs et liaisons Microsoft Graph dans Azure Functions.
services: functions
author: mattchenderson
manager: cfowler
editor: ''
ms.service: functions
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 12/20/2017
ms.author: mahender
ms.openlocfilehash: 2de80760484ae1869b340898ea1e5f740fbc2883
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2018
---
# <a name="microsoft-graph-bindings-for-azure-functions"></a>Liaisons Microsoft Graph pour Azure Functions

Cet article explique comment configurer et utiliser des déclencheurs et liaisons Microsoft Graph dans Azure Functions. Ces fonctions vous permettent d’utiliser Azure Functions pour travailler sur des données, des informations et des événements à partir de [Microsoft Graph](https://graph.microsoft.io).

L’extension Microsoft Graph fournit les liaisons suivantes :
- Une [liaison d’entrée de jeton d’authentification](#token-input) permet d’interagir avec toute API Microsoft Graph.
- Une [liaison d’entrée de tableau Excel](#excel-input) permet de lire des données à partir d’Excel.
- Une [liaison de sortie de tableau Excel](#excel-output) permet de modifier des données Excel.
- Une [liaison d’entrée de fichier OneDrive](#onedrive-input) permet de lire des fichiers à partir de OneDrive.
- Une [liaison de sortie de fichier OneDrive](#onedrive-output) permet d’écrire dans des fichiers sur OneDrive.
- Une [liaison de sortie de message Outlook](#outlook-output) permet d’envoyer du courrier via Outlook.
- Une collection de [déclencheurs et liaisons de webhook Microsoft Graph](#webhooks) permet de réagir à des événements de l’élément Microsoft Graph.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!Note]
> Les liaisons Microsoft Graph sont actuellement en préversion pour Azure Functions version 2.x. Elles ne sont pas prises en charge dans la version 1.x.

## <a name="packages"></a>Packages

La liaison d’entrée du jeton d’authentification est fournie dans le package NuGet [Microsoft.Azure.WebJobs.Extensions.AuthTokens](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.AuthTokens/). Les autres liaisons Microsoft Graph sont fournies dans le package [Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph/). Le code source du package se trouve dans le référentiel GitHub [azure-functions-microsoftgraph-extension](https://github.com/Azure/azure-functions-microsoftgraph-extension/).

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="setting-up-the-extensions"></a>Configuration des extensions

Les liaisons Microsoft Graph sont disponibles via des _extensions de liaison_. Les extensions de liaison sont des composants facultatifs du runtime Azure Functions. Cette section explique comment configurer l’élément Microsoft Graph et les extensions de jeton d’authentification.

### <a name="enabling-functions-20-preview"></a>Activation de la préversion d’Azure Functions 2.0

Les extensions de liaison sont uniquement disponibles pour la préversion d’Azure Functions 2.0. 

Pour plus d’informations sur la façon de configurer une application de fonction pour utiliser la préversion 2.0 du runtime de Functions, consultez [Comment cibler des versions du runtime Azure Functions](set-runtime-version.md).

### <a name="installing-the-extension"></a>Installation de l’extension

Pour installer une extension à partir du portail Azure, accédez à un modèle ou à une liaison qui y fait référence. Créez une fonction, puis dans l’écran de sélection de modèle, choisissez le scénario de « Microsoft Graph ». Sélectionnez l’un des modèles à partir de ce scénario. Vous pouvez également accéder à l’onglet « Intégrer » d’une fonction existante et sélectionner l’une des liaisons décrites dans cet article.

Dans les deux cas, un avertissement s’affiche, qui spécifie l’extension à installer. Cliquez sur **Installer** pour obtenir de l’extension. Chaque extension ne doit être installée qu’une seule fois par application de fonction. 

> [!Note] 
> Le processus d’installation dans le portail peut prendre jusqu'à 10 minutes d’un plan de consommation.

Si vous utilisez Visual Studio, vous pouvez obtenir les extensions en installant les [packages NuGet mentionnés plus haut dans cet article](#packages).

### <a name="configuring-authentication--authorization"></a>Configuration de l'authentification et des autorisations

Une identité est nécessaire pour pouvoir utiliser les liaisons décrites dans cet article. Cela permet à l’élément Microsoft Graph d’appliquer des autorisations et des interactions d’audit. L’identité peut être un utilisateur accédant à votre application ou à l’application proprement dite. Pour configurer cette identité, configurez [Authentification et autorisations App Service](https://docs.microsoft.com/azure/app-service/app-service-authentication-overview) avec Azure Active Directory. Vous devez également de demander les autorisations de ressources éventuelles que vos fonctions requièrent.

> [!Note] 
> L’extension Microsoft Graph ne prend en charge que l’authentification Azure AD. Les utilisateurs doivent se connecter avec un compte professionnel ou scolaire.

Si vous utilisez le portail Azure, un avertissement s’affichera sous l’invite pour installer l’extension. L’avertissement vous invite à configurer l’authentification et les autorisations App Service et à demander toute autorisation requise par le modèle ou la liaison. Cliquez sur **Configurer Azure AD maintenant** ou **Ajouter des autorisations maintenant** selon le cas.



<a name="token-input"></a>
## <a name="auth-token"></a>Jeton d'authentification

La liaison d’entrée de jeton d’authentification obtient un jeton Azure AD pour une ressource donnée, puis fournit celui-ci à votre code en tant que chaîne. La ressource peut être toute ressource pour laquelle l’application dispose d’autorisations. 

Cette section comporte les sous-sections suivantes :

* [Exemple](#auth-token---example)
* [Attributs](#auth-token---attributes)
* [Configuration](#auth-token---configuration)
* [Utilisation](#auth-token---usage)

### <a name="auth-token---example"></a>Jeton d’authentification - exemple

Consultez l’exemple propre à un langage particulier :

* [Script C# (.csx)](#auth-token---c-script-example)
* [JavaScript](#auth-token---javascript-example)

#### <a name="auth-token---c-script-example"></a>Jeton d’authentification - exemple de script C#

L’exemple suivant obtient les informations sur le profil utilisateur.

Le fichier *function.json* définit un déclencheur HTTP avec une liaison d’entrée de jeton :

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "token",
      "direction": "in",
      "name": "graphToken",
      "resource": "https://graph.microsoft.com",
      "identity": "userFromRequest"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Le code de script C# utilise le jeton pour effectuer un appel HTTP à l’élément Microsoft Graph, puis retourne le résultat :

```csharp
using System.Net; 
using System.Net.Http; 
using System.Net.Http.Headers; 

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, string graphToken, TraceWriter log)
{
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", graphToken);
    return await client.GetAsync("https://graph.microsoft.com/v1.0/me/");
}
```

#### <a name="auth-token---javascript-example"></a>Jeton d’authentification - exemple JavaScript

L’exemple suivant obtient les informations sur le profil utilisateur.

Le fichier *function.json* définit un déclencheur HTTP avec une liaison d’entrée de jeton :

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "token",
      "direction": "in",
      "name": "graphToken",
      "resource": "https://graph.microsoft.com",
      "identity": "userFromRequest"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Le code JavaScript utilise le jeton pour effectuer un appel HTTP à l’élément Microsoft Graph, puis retourne le résultat.

```js
const rp = require('request-promise');

module.exports = function (context, req) {
    let token = "Bearer " + context.bindings.graphToken;

    let options = {
        uri: 'https://graph.microsoft.com/v1.0/me/',
        headers: {
            'Authorization': token
        }
    };
    
    rp(options)
        .then(function(profile) {
            context.res = {
                body: profile
            };
            context.done();
        })
        .catch(function(err) {
            context.res = {
                status: 500,
                body: err
            };
            context.done();
        });
};
```

### <a name="auth-token---attributes"></a>Jeton d’authentification - attributs

Dans les [bibliothèques de classes C#](functions-dotnet-class-library.md), utilisez l’attribut [Token](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/TokenBinding/TokenAttribute.cs).

### <a name="auth-token---configuration"></a>Jeton d’authentification - configuration

Le tableau suivant décrit les propriétés de configuration de liaison que vous définissez dans le fichier *function.json* et l’attribut `Token`.

|Propriété function.json | Propriété d’attribut |Description|
|---------|---------|----------------------|
|**name**||Obligatoire : nom de variable utilisé dans le code de fonction pour le jeton d'authentification. Voir [Utilisation d’une liaison d’entrée de jeton d’authentification à partir du code](#token-input-code).|
|**type**||Obligatoire : doit être défini sur `token`.|
|**direction**||Obligatoire : doit être défini sur `in`.|
|**identity**|**Identité**|Obligatoire : identité utilisée pour effectuer l’action. Peut être l’une des valeurs suivantes :<ul><li><code>userFromRequest</code> : valide uniquement avec [déclencheur HTTP]. Utilise l’identité de l’utilisateur appelant.</li><li><code>userFromId</code> : utilise l’identité d’un utilisateur qui s’est précédemment connecté avec l’ID spécifié. Voir la propriété <code>userId</code>.</li><li><code>userFromToken</code> : utilise l’identité représentée par le jeton spécifié. Voir la propriété <code>userToken</code>.</li><li><code>clientCredentials</code> : utilise l’identité de l’application de fonction.</li></ul>|
|**userId**|**UserId**  |Obligatoire si et seulement si la propriété _identity_ a la valeur `userFromId`. ID principal associé à un utilisateur qui s’est précédemment connecté.|
|**userToken**|**UserToken**|Obligatoire si et seulement si la propriété _identity_ a la valeur `userFromToken`. Jeton valide pour l’application de fonction. |
|**Ressource**|**resource**|Obligatoire : URL de la ressource Azure AD pour laquelle le jeton est demandé.|

<a name="token-input-code"></a>
### <a name="auth-token---usage"></a>Jeton d’authentification - utilisation

La liaison elle-même ne nécessite pas d’autorisations Azure AD mais, selon la façon dont le jeton est utilisé, il se peut que vous deviez demander des autorisations supplémentaires. Vérifiez les exigences de la ressource à laquelle vous avez l’intention d’accéder avec le jeton.

Le jeton est toujours présenté au code sous forme de chaîne.




<a name="excel-input"></a>
## <a name="excel-input"></a>Entrée Excel

La liaison d’entrée de tableau Excel lit le contenu d’un tableau Excel stocké sur OneDrive.

Cette section comporte les sous-sections suivantes :

* [Exemple](#excel-input---example)
* [Attributs](#excel-input---attributes)
* [Configuration](#excel-input---configuration)
* [Utilisation](#excel-input---usage)

### <a name="excel-input---example"></a>Entrée Excel - exemple

Consultez l’exemple propre à un langage particulier :

* [Script C# (.csx)](#excel-input---c-script-example)
* [JavaScript](#excel-input---javascript-example)

#### <a name="excel-input---c-script-example"></a>Entrée Excel - exemple de script C#

Le fichier *function.json* suivant définit un déclencheur HTTP avec une liaison d’entrée Excel :

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "excel",
      "direction": "in",
      "name": "excelTableData",
      "path": "{query.workbook}",
      "identity": "UserFromRequest",
      "tableName": "{query.table}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Le code de script C# suivant lit le contenu du tableau spécifié, puis le retourne à l’utilisateur :

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives; 

public static IActionResult Run(HttpRequest req, string[][] excelTableData, TraceWriter log)
{
    return new OkObjectResult(excelTableData);
}
```

#### <a name="excel-input---javascript-example"></a>Entrée Excel - exemple JavaScript

Le fichier *function.json* suivant définit un déclencheur HTTP avec une liaison d’entrée Excel :

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "excel",
      "direction": "in",
      "name": "excelTableData",
      "path": "{query.workbook}",
      "identity": "UserFromRequest",
      "tableName": "{query.table}"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Le code JavaScript suivant lit le contenu du tableau spécifié, puis le retourne à l’utilisateur.

```js
module.exports = function (context, req) {
    context.res = {
        body: context.bindings.excelTableData
    };
    context.done();
};
```

### <a name="excel-input---attributes"></a>Entrée Excel - attributs

Dans les [bibliothèques de classes C#](functions-dotnet-class-library.md), utilisez l’attribut [Excel](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/ExcelAttribute.cs).

### <a name="excel-input---configuration"></a>Entrée Excel - configuration

Le tableau suivant décrit les propriétés de configuration de liaison que vous définissez dans le fichier *function.json* et l’attribut `Excel`.

|Propriété function.json | Propriété d’attribut |Description|
|---------|---------|----------------------|
|**name**||Obligatoire : nom de variable utilisé dans le code de fonction pour le tableau Excel. Voir [Utilisation d’une liaison d’entrée de tableau Excel à partir du code](#excel-input-code).|
|**type**||Obligatoire : doit être défini sur `excel`.|
|**direction**||Obligatoire : doit être défini sur `in`.|
|**identity**|**Identité**|Obligatoire : identité utilisée pour effectuer l’action. Peut être l’une des valeurs suivantes :<ul><li><code>userFromRequest</code> : valide uniquement avec [déclencheur HTTP]. Utilise l’identité de l’utilisateur appelant.</li><li><code>userFromId</code> : utilise l’identité d’un utilisateur qui s’est précédemment connecté avec l’ID spécifié. Voir la propriété <code>userId</code>.</li><li><code>userFromToken</code> : utilise l’identité représentée par le jeton spécifié. Voir la propriété <code>userToken</code>.</li><li><code>clientCredentials</code> : utilise l’identité de l’application de fonction.</li></ul>|
|**userId**|**UserId**  |Obligatoire si et seulement si la propriété _identity_ a la valeur `userFromId`. ID principal associé à un utilisateur qui s’est précédemment connecté.|
|**userToken**|**UserToken**|Obligatoire si et seulement si la propriété _identity_ a la valeur `userFromToken`. Jeton valide pour l’application de fonction. |
|**path**|**Chemin d’accès**|Obligatoire : chemin d’accès au classeur Excel sur OneDrive.|
|**worksheetName**|**WorksheetName**|La feuille de calcul dans laquelle se trouve le tableau.|
|**tableName**|**TableName**|Nom de la table. Si non spécifié, le contenu de la feuille de calcul est utilisé.|

<a name="excel-input-code"></a>
### <a name="excel-input---usage"></a>Entrée Excel - utilisation

Cette liaison nécessite les autorisations Azure AD suivantes :
|Ressource|Autorisation|
|--------|--------|
|Microsoft Graph|Lire les fichiers utilisateur|

La liaison expose les types suivants de fonctions .NET :
- string[][]
- Microsoft.Graph.WorkbookTable
- Types d’objets personnalisés (utilisant une liaison de modèle structurel)










<a name="excel-output"></a>
## <a name="excel-output"></a>Sortie Excel

La liaison de sortie Excel modifie le contenu d’un tableau Excel stocké sur OneDrive.

Cette section comporte les sous-sections suivantes :

* [Exemple](#excel-output---example)
* [Attributs](#excel-output---attributes)
* [Configuration](#excel-output---configuration)
* [Utilisation](#excel-output---usage)

### <a name="excel-output---example"></a>Sortie Excel - exemple

Consultez l’exemple propre à un langage particulier :

* [Script C# (.csx)](#excel-output---c-script-example)
* [JavaScript](#excel-output---javascript-example)

#### <a name="excel-output---c-script-example"></a>Sortie Excel - exemple de script C#

L’exemple suivant ajoute des lignes à un tableau Excel.

Le fichier *function.json* définit un déclencheur HTTP avec une liaison de sortie Excel :

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "newExcelRow",
      "type": "excel",
      "direction": "out",
      "identity": "userFromRequest",
      "updateType": "append",
      "path": "{query.workbook}",
      "tableName": "{query.table}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Le code de script C# ajoute une ligne au tableau (supposé ne contenir qu’une seule colonne) basée sur l’entrée de la chaîne de requête :

```csharp
using System.Net;
using System.Text;

public static async Task Run(HttpRequest req, IAsyncCollector<object> newExcelRow, TraceWriter log)
{
    string input = req.Query
        .FirstOrDefault(q => string.Compare(q.Key, "text", true) == 0)
        .Value;
    await newExcelRow.AddAsync(new {
        Text = input
        // Add other properties for additional columns here
    });
    return;
}
```

#### <a name="excel-output---javascript-example"></a>Sortie Excel - exemple JavaScript

L’exemple suivant ajoute des lignes à un tableau Excel.

Le fichier *function.json* définit un déclencheur HTTP avec une liaison de sortie Excel :

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "newExcelRow",
      "type": "excel",
      "direction": "out",
      "identity": "userFromRequest",
      "updateType": "append",
      "path": "{query.workbook}",
      "tableName": "{query.table}"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Le code JavaScript suivant ajoute une ligne au tableau (supposé ne contenir qu’une seule colonne) basée sur l’entrée de la chaîne de requête.

```js
module.exports = function (context, req) {
    context.bindings.newExcelRow = {
        text: req.query.text
        // Add other properties for additional columns here
    }
    context.done();
};
```

### <a name="excel-output---attributes"></a>Sortie Excel - attributs

Dans les [bibliothèques de classes C#](functions-dotnet-class-library.md), utilisez l’attribut [Excel](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/ExcelAttribute.cs).

### <a name="excel-output---configuration"></a>Sortie Excel - configuration

Le tableau suivant décrit les propriétés de configuration de liaison que vous définissez dans le fichier *function.json* et l’attribut `Excel`.

|Propriété function.json | Propriété d’attribut |Description|
|---------|---------|----------------------|
|**name**||Obligatoire : nom de variable utilisé dans le code de fonction pour le jeton d'authentification. Voir [Utilisation d’une liaison de sortie de tableau Excel à partir du code](#excel-output-code).|
|**type**||Obligatoire : doit être défini sur `excel`.|
|**direction**||Obligatoire : doit être défini sur `out`.|
|**identity**|**Identité**|Obligatoire : identité utilisée pour effectuer l’action. Peut être l’une des valeurs suivantes :<ul><li><code>userFromRequest</code> : valide uniquement avec [déclencheur HTTP]. Utilise l’identité de l’utilisateur appelant.</li><li><code>userFromId</code> : utilise l’identité d’un utilisateur qui s’est précédemment connecté avec l’ID spécifié. Voir la propriété <code>userId</code>.</li><li><code>userFromToken</code> : utilise l’identité représentée par le jeton spécifié. Voir la propriété <code>userToken</code>.</li><li><code>clientCredentials</code> : utilise l’identité de l’application de fonction.</li></ul>|
|**UserId** |**userId** |Obligatoire si et seulement si la propriété _identity_ a la valeur `userFromId`. ID principal associé à un utilisateur qui s’est précédemment connecté.|
|**userToken**|**UserToken**|Obligatoire si et seulement si la propriété _identity_ a la valeur `userFromToken`. Jeton valide pour l’application de fonction. |
|**path**|**Chemin d’accès**|Obligatoire : chemin d’accès au classeur Excel sur OneDrive.|
|**worksheetName**|**WorksheetName**|La feuille de calcul dans laquelle se trouve le tableau.|
|**tableName**|**TableName**|Nom de la table. Si non spécifié, le contenu de la feuille de calcul est utilisé.|
|**updateType**|**UpdateType**|Obligatoire : type de modification à apporter au tableau. Peut être l’une des valeurs suivantes :<ul><li><code>update</code> : remplace le contenu du tableau sur OneDrive.</li><li><code>append</code> : ajoute la charge utile à la fin du tableau sur OneDrive en créant des lignes.</li></ul>|

<a name="excel-output-code"></a>
### <a name="excel-output---usage"></a>Sortie Excel - utilisation

Cette liaison nécessite les autorisations Azure AD suivantes :
|Ressource|Autorisation|
|--------|--------|
|Microsoft Graph|Avoir un accès total aux fichiers utilisateur|

La liaison expose les types suivants de fonctions .NET :
- string[][]
- Newtonsoft.Json.Linq.JObject
- Microsoft.Graph.WorkbookTable
- Types d’objets personnalisés (utilisant une liaison de modèle structurel)





<a name="onedrive-input"></a>
## <a name="file-input"></a>Entrée de fichier

La liaison d’entrée de fichier OneDrive lit le contenu d’un fichier stocké sur OneDrive.

Cette section comporte les sous-sections suivantes :

* [Exemple](#file-input---example)
* [Attributs](#file-input---attributes)
* [Configuration](#file-input---configuration)
* [Utilisation](#file-input---usage)

### <a name="file-input---example"></a>Entrée de fichier - exemple

Consultez l’exemple propre à un langage particulier :

* [Script C# (.csx)](#file-input---c-script-example)
* [JavaScript](#file-input---javascript-example)

#### <a name="file-input---c-script-example"></a>Entrée de fichier - exemple de script C#

L’exemple suivant lit un fichier stocké sur OneDrive.

Le fichier *function.json* définit un déclencheur HTTP avec une liaison d’entrée de fichier OneDrive :

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "myOneDriveFile",
      "type": "onedrive",
      "direction": "in",
      "path": "{query.filename}",
      "identity": "userFromRequest"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Le code de script C# lit le fichier spécifié dans la chaîne de requête et enregistre sa longueur :

```csharp
using System.Net;

public static void Run(HttpRequestMessage req, Stream myOneDriveFile, TraceWriter log)
{
    log.Info(myOneDriveFile.Length.ToString());
}
```

#### <a name="file-input---javascript-example"></a>Entrée de fichier - exemple JavaScript

L’exemple suivant lit un fichier stocké sur OneDrive.

Le fichier *function.json* définit un déclencheur HTTP avec une liaison d’entrée de fichier OneDrive :

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "myOneDriveFile",
      "type": "onedrive",
      "direction": "in",
      "path": "{query.filename}",
      "identity": "userFromRequest"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Le code JavaScript suivant lit le fichier spécifié dans la chaîne de requête et retourne sa longueur.

```js
module.exports = function (context, req) {
    context.res = {
        body: context.bindings.myOneDriveFile.length
    };
    context.done();
};
```

### <a name="file-input---attributes"></a>Entrée de fichier - attributs

Dans les [bibliothèques de classes C#](functions-dotnet-class-library.md), utilisez l’attribut [OneDrive](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OneDriveAttribute.cs).

### <a name="file-input---configuration"></a>Entrée de fichier - configuration

Le tableau suivant décrit les propriétés de configuration de liaison que vous définissez dans le fichier *function.json* et l’attribut `OneDrive`.

|Propriété function.json | Propriété d’attribut |Description|
|---------|---------|----------------------|
|**name**||Obligatoire : nom de variable utilisé dans le code de fonction pour le fichier. Voir [Utilisation d’une liaison d’entrée de fichier OneDrive à partir du code](#onedrive-input-code).|
|**type**||Obligatoire : doit être défini sur `onedrive`.|
|**direction**||Obligatoire : doit être défini sur `in`.|
|**identity**|**Identité**|Obligatoire : identité utilisée pour effectuer l’action. Peut être l’une des valeurs suivantes :<ul><li><code>userFromRequest</code> : valide uniquement avec [déclencheur HTTP]. Utilise l’identité de l’utilisateur appelant.</li><li><code>userFromId</code> : utilise l’identité d’un utilisateur qui s’est précédemment connecté avec l’ID spécifié. Voir la propriété <code>userId</code>.</li><li><code>userFromToken</code> : utilise l’identité représentée par le jeton spécifié. Voir la propriété <code>userToken</code>.</li><li><code>clientCredentials</code> : utilise l’identité de l’application de fonction.</li></ul>|
|**userId**|**UserId**  |Obligatoire si et seulement si la propriété _identity_ a la valeur `userFromId`. ID principal associé à un utilisateur qui s’est précédemment connecté.|
|**userToken**|**UserToken**|Obligatoire si et seulement si la propriété _identity_ a la valeur `userFromToken`. Jeton valide pour l’application de fonction. |
|**path**|**Chemin d’accès**|Obligatoire : chemin d’accès au fichier sur OneDrive.|

<a name="onedrive-input-code"></a>
### <a name="file-input---usage"></a>Entrée de fichier - utilisation

Cette liaison nécessite les autorisations Azure AD suivantes :
|Ressource|Autorisation|
|--------|--------|
|Microsoft Graph|Lire les fichiers utilisateur|

La liaison expose les types suivants de fonctions .NET :
- byte[]
- Stream
- chaîne
- Microsoft.Graph.DriveItem






<a name="onedrive-output"></a>
## <a name="file-output"></a>Sortie de fichier

La liaison de sortie de fichier OneDrive modifie le contenu d’un fichier stocké sur OneDrive.

Cette section comporte les sous-sections suivantes :

* [Exemple](#file-output---example)
* [Attributs](#file-output---attributes)
* [Configuration](#file-output---configuration)
* [Utilisation](#file-output---usage)

### <a name="file-output---example"></a>Sortie de fichier - exemple

Consultez l’exemple propre à un langage particulier :

* [Script C# (.csx)](#file-output---c-script-example)
* [JavaScript](#file-output---javascript-example)

#### <a name="file-output---c-script-example"></a>Sortie de fichier - exemple de script C#

L’exemple suivant écrit dans un fichier stocké sur OneDrive.

Le fichier *function.json* définit un déclencheur HTTP avec une liaison de sortie OneDrive :

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "myOneDriveFile",
      "type": "onedrive",
      "direction": "out",
      "path": "FunctionsTest.txt",
      "identity": "userFromRequest"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Le code de script C# obtient le texte de la chaîne de requête et écrit ce texte dans un fichier texte (FunctionsTest.txt, tel que défini dans l’exemple précédent) à la racine du OneDrive de l’appelant :

```csharp
using System.Net;
using System.Text;

public static async Task Run(HttpRequest req, TraceWriter log, Stream myOneDriveFile)
{
    string data = req.Query
        .FirstOrDefault(q => string.Compare(q.Key, "text", true) == 0)
        .Value;
    await myOneDriveFile.WriteAsync(Encoding.UTF8.GetBytes(data), 0, data.Length);
    return;
}
```

#### <a name="file-output---javascript-example"></a>Sortie de fichier - exemple JavaScript

L’exemple suivant écrit dans un fichier stocké sur OneDrive.

Le fichier *function.json* définit un déclencheur HTTP avec une liaison de sortie OneDrive :

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "myOneDriveFile",
      "type": "onedrive",
      "direction": "out",
      "path": "FunctionsTest.txt",
      "identity": "userFromRequest"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Le code JavaScript obtient le texte de la chaîne de requête et écrit ce texte dans un fichier texte (FunctionsTest.txt, tel que défini dans la configuration ci-dessus) à la racine du OneDrive de l’appelant.

```js
module.exports = function (context, req) {
    context.bindings.myOneDriveFile = req.query.text;
    context.done();
};
```

### <a name="file-output---attributes"></a>Sortie de fichier - attributs

Dans les [bibliothèques de classes C#](functions-dotnet-class-library.md), utilisez l’attribut [OneDrive](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OneDriveAttribute.cs).

### <a name="file-output---configuration"></a>Sortie de fichier - configuration

Le tableau suivant décrit les propriétés de configuration de liaison que vous définissez dans le fichier *function.json* et l’attribut `OneDrive`.

|Propriété function.json | Propriété d’attribut |Description|
|---------|---------|----------------------|
|**name**||Obligatoire : nom de variable utilisé dans le code de fonction pour le fichier. Voir [Utilisation d’une liaison de sortie de fichier OneDrive à partir du code](#onedrive-output-code).|
|**type**||Obligatoire : doit être défini sur `onedrive`.|
|**direction**||Obligatoire : doit être défini sur `out`.|
|**identity**|**Identité**|Obligatoire : identité utilisée pour effectuer l’action. Peut être l’une des valeurs suivantes :<ul><li><code>userFromRequest</code> : valide uniquement avec [déclencheur HTTP]. Utilise l’identité de l’utilisateur appelant.</li><li><code>userFromId</code> : utilise l’identité d’un utilisateur qui s’est précédemment connecté avec l’ID spécifié. Voir la propriété <code>userId</code>.</li><li><code>userFromToken</code> : utilise l’identité représentée par le jeton spécifié. Voir la propriété <code>userToken</code>.</li><li><code>clientCredentials</code> : utilise l’identité de l’application de fonction.</li></ul>|
|**UserId** |**userId** |Obligatoire si et seulement si la propriété _identity_ a la valeur `userFromId`. ID principal associé à un utilisateur qui s’est précédemment connecté.|
|**userToken**|**UserToken**|Obligatoire si et seulement si la propriété _identity_ a la valeur `userFromToken`. Jeton valide pour l’application de fonction. |
|**path**|**Chemin d’accès**|Obligatoire : chemin d’accès au fichier sur OneDrive.|

<a name="onedrive-output-code"></a>
#### <a name="file-output---usage"></a>Sortie de fichier - utilisation

Cette liaison nécessite les autorisations Azure AD suivantes :
|Ressource|Autorisation|
|--------|--------|
|Microsoft Graph|Avoir un accès total aux fichiers utilisateur|

La liaison expose les types suivants de fonctions .NET :
- byte[]
- Stream
- chaîne
- Microsoft.Graph.DriveItem





<a name="outlook-output"></a>
## <a name="outlook-output"></a>Sortie Outlook

La liaison de sortie de message Outlook envoie un message électronique via Outlook.

Cette section comporte les sous-sections suivantes :

* [Exemple](#outlook-output---example)
* [Attributs](#outlook-output---attributes)
* [Configuration](#outlook-output---configuration)
* [Utilisation](#outlook-outnput---usage)

### <a name="outlook-output---example"></a>Sortie Outlook - exemple

Consultez l’exemple propre à un langage particulier :

* [Script C# (.csx)](#outlook-output---c-script-example)
* [JavaScript](#outlook-output---javascript-example)

#### <a name="outlook-output---c-script-example"></a>Sortie Outlook - exemple de script C#

L’exemple suivant envoie un message électronique via Outlook.

Le fichier *function.json* définit un déclencheur HTTP avec une liaison de sortie de message Outlook :

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "message",
      "type": "outlook",
      "direction": "out",
      "identity": "userFromRequest"
    }
  ],
  "disabled": false
}
```

Le code de script C# envoie un e-mail de l’appelant à un destinataire spécifié dans la chaîne de requête :

```csharp
using System.Net;

public static void Run(HttpRequest req, out Message message, TraceWriter log)
{ 
    string emailAddress = req.Query["to"];
    message = new Message(){
        subject = "Greetings",
        body = "Sent from Azure Functions",
        recipient = new Recipient() {
            address = emailAddress
        }
    };
}

public class Message {
    public String subject {get; set;}
    public String body {get; set;}
    public Recipient recipient {get; set;}
}

public class Recipient {
    public String address {get; set;}
    public String name {get; set;}
}
```

#### <a name="outlook-output---javascript-example"></a>Sortie Outlook - exemple JavaScript

L’exemple suivant envoie un message électronique via Outlook.

Le fichier *function.json* définit un déclencheur HTTP avec une liaison de sortie de message Outlook :

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "message",
      "type": "outlook",
      "direction": "out",
      "identity": "userFromRequest"
    }
  ],
  "disabled": false
}
```

Le code JavaScript envoie un e-mail de l’appelant à un destinataire spécifié dans la chaîne de requête :

```js
module.exports = function (context, req) {
    context.bindings.message = {
        subject: "Greetings",
        body: "Sent from Azure Functions with JavaScript",
        recipient: {
            address: req.query.to 
        } 
    };
    context.done();
};
```

### <a name="outlook-output---attributes"></a>Sortie Outlook - attributs

Dans les [bibliothèques de classes C#](functions-dotnet-class-library.md), utilisez l’attribut [Outlook](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OutlookAttribute.cs).

### <a name="outlook-output---configuration"></a>Sortie Outlook - configuration

Le tableau suivant décrit les propriétés de configuration de liaison que vous définissez dans le fichier *function.json* et l’attribut `Outlook`.

|Propriété function.json | Propriété d’attribut |Description|
|---------|---------|----------------------|
|**name**||Obligatoire : nom de variable utilisé dans le code de fonction pour l’e-mail. Voir [Utilisation d’une liaison de sortie de message Outlook à partir du code](#outlook-output-code).|
|**type**||Obligatoire : doit être défini sur `outlook`.|
|**direction**||Obligatoire : doit être défini sur `out`.|
|**identity**|**Identité**|Obligatoire : identité utilisée pour effectuer l’action. Peut être l’une des valeurs suivantes :<ul><li><code>userFromRequest</code> : valide uniquement avec [déclencheur HTTP]. Utilise l’identité de l’utilisateur appelant.</li><li><code>userFromId</code> : utilise l’identité d’un utilisateur qui s’est précédemment connecté avec l’ID spécifié. Voir la propriété <code>userId</code>.</li><li><code>userFromToken</code> : utilise l’identité représentée par le jeton spécifié. Voir la propriété <code>userToken</code>.</li><li><code>clientCredentials</code> : utilise l’identité de l’application de fonction.</li></ul>|
|**userId**|**UserId**  |Obligatoire si et seulement si la propriété _identity_ a la valeur `userFromId`. ID principal associé à un utilisateur qui s’est précédemment connecté.|
|**userToken**|**UserToken**|Obligatoire si et seulement si la propriété _identity_ a la valeur `userFromToken`. Jeton valide pour l’application de fonction. |

<a name="outlook-output-code"></a>
### <a name="outlook-output---usage"></a>Sortie Outlook - utilisation

Cette liaison nécessite les autorisations Azure AD suivantes :
|Ressource|Autorisation|
|--------|--------|
|Microsoft Graph|Envoyer un e-mail en tant qu’utilisateur|

La liaison expose les types suivants de fonctions .NET :
- Microsoft.Graph.Message
- Newtonsoft.Json.Linq.JObject
- chaîne
- Types d’objets personnalisés (utilisant une liaison de modèle structurel)






## <a name="webhooks"></a>Webhooks

Les webhooks vous permettent de réagir à événements dans l’élément Microsoft Graph. Pour prendre en charge les webhooks, des fonctions sont requises pour créer et actualiser les _webhook abonnements_, ainsi qu’y réagir. Une solution webhook complète nécessite une combinaison des liaisons suivantes :
- Un [déclencheur de webhook Microsoft Graph](#webhook-trigger) vous permet de réagir à un webhook entrant.
- Une [liaison d’entrée d’abonnement webhook Microsoft Graph](#webhook-input) permet de répertorier les abonnements existants et éventuellement de les actualiser.
- Une [liaison de sortie d’abonnement webhook Microsoft Graph](#webhook-output) permet de créer ou supprimer des abonnements webhook.

Les liaisons proprement dites ne nécessitent pas d’autorisations Azure AD, mais vous devez demander des autorisations appropriées pour le type de ressource auquel vous souhaitez réagir. Pour la liste des autorisations nécessaires pour chaque type de ressource, voir [Autorisations](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/subscription_post_subscriptions#permissions).

Pour plus d’informations sur les webhooks, consultez [Utilisation de webhooks dans Microsoft Graph].





## <a name="webhook-trigger"></a>Déclencheur de webhook

Le déclencheur de webhook Microsoft Graph permet à une fonction de réagir à un webhook entrant à partir de l’élément Microsoft Graph. Chaque instance de ce déclencheur peut réagir à un seul type de ressource Microsoft Graph.

Cette section comporte les sous-sections suivantes :

* [Exemple](#webhook-trigger---example)
* [Attributs](#webhook-trigger---attributes)
* [Configuration](#webhook-trigger---configuration)
* [Utilisation](#webhook-trigger---usage)

### <a name="webhook-trigger---example"></a>Déclencheur webhook - exemple

Consultez l’exemple propre à un langage particulier :

* [Script C# (.csx)](#webhook-trigger---c-script-example)
* [JavaScript](#webhook-trigger---javascript-example)

#### <a name="webhook-trigger---c-script-example"></a>Déclencheur de webhook - exemple de script C#

L’exemple suivant gère les webhooks pour les messages entrants Outlook. Pour utiliser un déclencheur de webhook, vous devez [créer un abonnement](#webhook-output---example) et pouvez [actualiser l’abonnement](#webhook-subscription-refresh) pour éviter qu’il n’expire.

Le fichier *function.json* définit un déclencheur de webhook :

```json
{
  "bindings": [
    {
      "name": "msg",
      "type": "GraphWebhookTrigger",
      "direction": "in",
      "resourceType": "#Microsoft.Graph.Message"
    }
  ],
  "disabled": false
}
```

Le code de script C# réagit aux e-mails entrants et enregistre le corps de ceux qui sont envoyés par le destinataire et dont l’objet contient la mention « Azure Functions » :

```csharp
#r "Microsoft.Graph"
using Microsoft.Graph;
using System.Net;

public static async Task Run(Message msg, TraceWriter log)  
{
    log.Info("Microsoft Graph webhook trigger function processed a request.");

    // Testable by sending oneself an email with the subject "Azure Functions" and some text body
    if (msg.Subject.Contains("Azure Functions") && msg.From.Equals(msg.Sender)) {
        log.Info($"Processed email: {msg.BodyPreview}");
    }
}
```

#### <a name="webhook-trigger---javascript-example"></a>Déclencheur de webhook - exemple JavaScript

L’exemple suivant gère les webhooks pour les messages entrants Outlook. Pour utiliser un déclencheur de webhook, vous devez [créer un abonnement](#webhook-output---example) et pouvez [actualiser l’abonnement](#webhook-subscription-refresh) pour éviter qu’il n’expire.

Le fichier *function.json* définit un déclencheur de webhook :

```json
{
  "bindings": [
    {
      "name": "msg",
      "type": "GraphWebhookTrigger",
      "direction": "in",
      "resourceType": "#Microsoft.Graph.Message"
    }
  ],
  "disabled": false
}
```

Le code JavaScript réagit aux e-mails entrants et enregistre le corps de ceux qui sont envoyés par le destinataire et dont l’objet contient la mention « Azure Functions » :

```js
module.exports = function (context) {
    context.log("Microsoft Graph webhook trigger function processed a request.");
    const msg = context.bindings.msg
    // Testable by sending oneself an email with the subject "Azure Functions" and some text body
    if((msg.subject.indexOf("Azure Functions") > -1) && (msg.from === msg.sender) ) {
      context.log(`Processed email: ${msg.bodyPreview}`);
    }
    context.done();
};
```

### <a name="webhook-trigger---attributes"></a>Déclencheur de webhook - attributs

Dans les [bibliothèques de classes C#](functions-dotnet-class-library.md), utilisez l’attribut [GraphWebHookTrigger](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebHookTriggerAttribute.cs).

### <a name="webhook-trigger---configuration"></a>Déclencheur de webhook - configuration

Le tableau suivant décrit les propriétés de configuration de liaison que vous définissez dans le fichier *function.json* et l’attribut `GraphWebHookTrigger`.

|Propriété function.json | Propriété d’attribut |Description|
|---------|---------|----------------------|
|**name**||Obligatoire : nom de variable utilisé dans le code de fonction pour l’e-mail. Voir [Utilisation d’une liaison de sortie de message Outlook à partir du code](#outlook-output-code).|
|**type**||Obligatoire : doit être défini sur `graphWebhook`.|
|**direction**||Obligatoire : doit être défini sur `trigger`.|
|**resourceType**|**ResourceType**|Obligatoire : ressource graphique pour laquelle cette fonction doit répondre aux webhooks. Peut être l’une des valeurs suivantes :<ul><li><code>#Microsoft.Graph.Message</code> : modifications apportées aux messages Outlook.</li><li><code>#Microsoft.Graph.DriveItem</code> : modifications apportées aux éléments racine de OneDrive.</li><li><code>#Microsoft.Graph.Contact</code> : modifications apportées aux contacts personnels dans Outlook.</li><li><code>#Microsoft.Graph.Event</code> : modifications apportées aux éléments du calendrier Outlook.</li></ul>|

> [!Note]
> Une application de fonction ne peut avoir qu’une seule fonction inscrite par rapport à une valeur `resourceType` donnée.

### <a name="webhook-trigger---usage"></a>Déclencheur de webhook - utilisation

La liaison expose les types suivants de fonctions .NET :
- Types de Kit SDK Microsoft Graph pertinents pour le type de ressource, tels que `Microsoft.Graph.Message` ou `Microsoft.Graph.DriveItem`.
- Types d’objets personnalisés (utilisant une liaison de modèle structurel)




<a name="webhook-input"></a>
## <a name="webhook-input"></a>Entrée de webhook

La liaison d’entrée de webhook Microsoft Graph permet de récupérer la liste des abonnements gérés par cette application de fonction. La liaison lit à partir d’un stockage d’applications de fonction, et ne reflète donc pas les autres abonnements créés en dehors de l’application.

Cette section comporte les sous-sections suivantes :

* [Exemple](#webhook-input---example)
* [Attributs](#webhook-input---attributes)
* [Configuration](#webhook-input---configuration)
* [Utilisation](#webhook-input---usage)

### <a name="webhook-input---example"></a>Entrée de webhook - exemple

Consultez l’exemple propre à un langage particulier :

* [Script C# (.csx)](#webhook-input---c-script-example)
* [JavaScript](#webhook-input---javascript-example)

#### <a name="webhook-input---c-script-example"></a>Entrée de webhook - exemple de script C#

L’exemple suivant obtient tous les abonnements pour l’utilisateur appelant et les supprime.

Le fichier *function.json* définit un déclencheur HTTP avec une liaison d’entrée d’abonnement et une liaison de sortie d’abonnement qui utilise l’action delete :

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "existingSubscriptions",
      "direction": "in",
      "filter": "userFromRequest"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "subscriptionsToDelete",
      "direction": "out",
      "action": "delete",
      "identity": "userFromRequest"
    },
    {
      "type": "http",
      "name": "res",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Le code de script C# obtient les abonnements et les supprime :

```csharp
using System.Net;

public static async Task Run(HttpRequest req, string[] existingSubscriptions, IAsyncCollector<string> subscriptionsToDelete, TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");
    foreach (var subscription in existingSubscriptions)
    {
        log.Info($"Deleting subscription {subscription}");
        await subscriptionsToDelete.AddAsync(subscription);
    }
}
```

#### <a name="webhook-input---javascript-example"></a>Entrée de webhook - exemple JavaScript

L’exemple suivant obtient tous les abonnements pour l’utilisateur appelant et les supprime.

Le fichier *function.json* définit un déclencheur HTTP avec une liaison d’entrée d’abonnement et une liaison de sortie d’abonnement qui utilise l’action delete :

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "existingSubscriptions",
      "direction": "in",
      "filter": "userFromRequest"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "subscriptionsToDelete",
      "direction": "out",
      "action": "delete",
      "identity": "userFromRequest"
    },
    {
      "type": "http",
      "name": "res",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Le code JavaScript obtient les abonnements et les supprime :

```js
module.exports = function (context, req) {
    const existing = context.bindings.existingSubscriptions;
    var toDelete = [];
    for (var i = 0; i < existing.length; i++) {
        context.log(`Deleting subscription ${existing[i]}`);
        todelete.push(existing[i]);
    }
    context.bindings.subscriptionsToDelete = toDelete;
    context.done();
};
```

### <a name="webhook-input---attributes"></a>Entrée de webhook - attributs

Dans les [bibliothèques de classes C#](functions-dotnet-class-library.md), utilisez l’attribut [GraphWebHookSubscription](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebHookSubscriptionAttribute.cs).

### <a name="webhook-input---configuration"></a>Entrée de webhook - configuration

Le tableau suivant décrit les propriétés de configuration de liaison que vous définissez dans le fichier *function.json* et l’attribut `GraphWebHookSubscription`.

|Propriété function.json | Propriété d’attribut |Description|
|---------|---------|----------------------|
|**name**||Obligatoire : nom de variable utilisé dans le code de fonction pour l’e-mail. Voir [Utilisation d’une liaison de sortie de message Outlook à partir du code](#outlook-output-code).|
|**type**||Obligatoire : doit être défini sur `graphWebhookSubscription`.|
|**direction**||Obligatoire : doit être défini sur `in`.|
|**filter**|**Filter**| Si la valeur est définie sur `userFromRequest`, la liaison extrait uniquement les abonnements appartenant à l’utilisateur appelant (valide uniquement avec un [déclencheur HTTP]).| 

### <a name="webhook-input---usage"></a>Entrée de webhook - utilisation

La liaison expose les types suivants de fonctions .NET :
- string[]
- Tableaux de types d’objets personnalisés
- Newtonsoft.Json.Linq.JObject[]
- Microsoft.Graph.Subscription[]





## <a name="webhook-output"></a>Sortie de webhook

La liaison de sortie d’abonnement webhook permet de créer, de supprimer et d’actualiser des abonnements webhook dans l’élément Microsoft Graph.

Cette section comporte les sous-sections suivantes :

* [Exemple](#webhook-output---example)
* [Attributs](#webhook-output---attributes)
* [Configuration](#webhook-output---configuration)
* [Utilisation](#webhook-output---usage)

### <a name="webhook-output---example"></a>Sortie d’abonnement - exemple

Consultez l’exemple propre à un langage particulier :

* [Script C# (.csx)](#webhook-output---c-script-example)
* [JavaScript](#webhook-output---javascript-example)

#### <a name="webhook-output---c-script-example"></a>Sortie de webhook - exemple de script C#

L’exemple suivant permet de créer un abonnement. Vous pouvez [actualiser l’abonnement](#webhook-subscription-refresh) pour éviter qu’il n’expire.

Le fichier *function.json* définit un déclencheur HTTP avec une liaison de sortie d’abonnement en utilisant l’action create :

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "clientState",
      "direction": "out",
      "action": "create",
      "subscriptionResource": "me/mailFolders('Inbox')/messages",
      "changeTypes": [
        "created"
      ],
      "identity": "userFromRequest"
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Le code de script C# inscrit un webhook qui informe cette application de fonction quand l’utilisateur appelant reçoit un message Outlook :

```csharp
using System;
using System.Net;

public static HttpResponseMessage run(HttpRequestMessage req, out string clientState, TraceWriter log)
{
  log.Info("C# HTTP trigger function processed a request.");
    clientState = Guid.NewGuid().ToString();
    return new HttpResponseMessage(HttpStatusCode.OK);
}
```

#### <a name="webhook-output---javascript-example"></a>Sortie de webhook - exemple JavaScript

L’exemple suivant permet de créer un abonnement. Vous pouvez [actualiser l’abonnement](#webhook-subscription-refresh) pour éviter qu’il n’expire.

Le fichier *function.json* définit un déclencheur HTTP avec une liaison de sortie d’abonnement en utilisant l’action create :

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "clientState",
      "direction": "out",
      "action": "create",
      "subscriptionResource": "me/mailFolders('Inbox')/messages",
      "changeTypes": [
        "created"
      ],
      "identity": "userFromRequest"
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Le code JavaScript inscrit un webhook qui informe cette application de fonction quand l’utilisateur appelant reçoit un message Outlook :

```js
const uuidv4 = require('uuid/v4');

module.exports = function (context, req) {
    context.bindings.clientState = uuidv4();
    context.done();
};
```

### <a name="webhook-output---attributes"></a>Sortie de webhook - attributs

Dans les [bibliothèques de classes C#](functions-dotnet-class-library.md), utilisez l’attribut [GraphWebHookSubscription](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebHookSubscriptionAttribute.cs).

### <a name="webhook-output---configuration"></a>Sortie de webhook - configuration

Le tableau suivant décrit les propriétés de configuration de liaison que vous définissez dans le fichier *function.json* et l’attribut `GraphWebHookSubscription`.

|Propriété function.json | Propriété d’attribut |Description|
|---------|---------|----------------------|
|**name**||Obligatoire : nom de variable utilisé dans le code de fonction pour l’e-mail. Voir [Utilisation d’une liaison de sortie de message Outlook à partir du code](#outlook-output-code).|
|**type**||Obligatoire : doit être défini sur `graphWebhookSubscription`.|
|**direction**||Obligatoire : doit être défini sur `out`.|
|**identity**|**Identité**|Obligatoire : identité utilisée pour effectuer l’action. Peut être l’une des valeurs suivantes :<ul><li><code>userFromRequest</code> : valide uniquement avec [déclencheur HTTP]. Utilise l’identité de l’utilisateur appelant.</li><li><code>userFromId</code> : utilise l’identité d’un utilisateur qui s’est précédemment connecté avec l’ID spécifié. Voir la propriété <code>userId</code>.</li><li><code>userFromToken</code> : utilise l’identité représentée par le jeton spécifié. Voir la propriété <code>userToken</code>.</li><li><code>clientCredentials</code> : utilise l’identité de l’application de fonction.</li></ul>|
|**userId**|**UserId**  |Obligatoire si et seulement si la propriété _identity_ a la valeur `userFromId`. ID principal associé à un utilisateur qui s’est précédemment connecté.|
|**userToken**|**UserToken**|Obligatoire si et seulement si la propriété _identity_ a la valeur `userFromToken`. Jeton valide pour l’application de fonction. |
|**action**|**Action**|Obligatoire : spécifie l’action que la liaison doit effectuer. Peut être l’une des valeurs suivantes :<ul><li><code>create</code> : inscrit un nouvel abonnement.</li><li><code>delete</code> : supprime un abonnement spécifié.</li><li><code>refresh</code> : actualise un abonnement spécifié pour empêcher son expiration.</li></ul>|
|**subscriptionResource**|**SubscriptionResource**|Obligatoire si et seulement si la propriété _action_ a la valeur `create`. Spécifie la ressource Microsoft Graph dont les modifications doivent être surveillées. Voir [Utilisation de webhooks dans Microsoft Graph]. |
|**changeType**|**ChangeType**|Obligatoire si et seulement si la propriété _action_ a la valeur `create`. Indique le type de modification de la ressource d’abonnement qui déclenche une notification. Les valeurs prises en charge sont les suivantes : `created`, `updated`, `deleted`. Plusieurs valeurs peuvent être combinées à l’aide d’une liste séparée par des virgules.|

### <a name="webhook-output---usage"></a>Sortie de webhook - utilisation

La liaison expose les types suivants de fonctions .NET :
- chaîne
- Microsoft.Graph.Subscription




<a name="webhook-examples"></a>
## <a name="webhook-subscription-refresh"></a>Actualisation de l’abonnement webhook

Il existe deux approches de l’actualisation d’abonnements :

- Utiliser l’identité de l’application pour gérer tous les abonnements. Cette opération nécessite le consentement d’un administrateur Azure Active Directory. Elle peut être utilisée par tous les langages qu’Azure Functions prend en charge.
- Utiliser l’identité associée à chaque abonnement en liant manuellement chaque ID utilisateur. Un code personnalisé est nécessaire pour établir la liaison. Cette approche ne peut être utilisée que par des fonctions .NET.

Cette section offre un exemple de chacune de ces approches :

* [Exemple d’identité d’application](#webhook-subscription-refresh---app-identity-example)
* [Exemple d’identité d’utilisateur](#webhook-subscription-refresh---user-identity-example)

### <a name="webhook-subscription-refresh---app-identity-example"></a>Actualisation de l’abonnement webhook - exemple d’identité d’application

Consultez l’exemple propre à un langage particulier :

* [Script C# (.csx)](#app-identity-refresh---c-script-example)
* [JavaScript](#app-identity-refresh---javascript-example)

### <a name="app-identity-refresh---c-script-example"></a>Actualisation d’identité d’application - exemple de script C#

L’exemple suivant utilise l’identité de l’application pour actualiser un abonnement.

Le fichier *function.json* définit un déclencheur de minuteur avec une liaison d’entrée d’abonnement et une liaison de sortie d’abonnement :

```json
{
  "bindings": [
    {
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in",
      "schedule": "0 * * */2 * *"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "existingSubscriptions",
      "direction": "in"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "subscriptionsToRefresh",
      "direction": "out",
      "action": "refresh",
      "identity": "clientCredentials"
    }
  ],
  "disabled": false
}
```

Le code de script C# actualise les abonnements :

```csharp
using System;

public static void Run(TimerInfo myTimer, string[] existingSubscriptions, ICollector<string> subscriptionsToRefresh, TraceWriter log)
{
    // This template uses application permissions and requires consent from an Azure Active Directory admin.
    // See https://go.microsoft.com/fwlink/?linkid=858780
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
    foreach (var subscription in existingSubscriptions)
    {
      log.Info($"Refreshing subscription {subscription}");
      subscriptionsToRefresh.Add(subscription);
    }
}
```

### <a name="app-identity-refresh---c-script-example"></a>Actualisation d’identité d’application - exemple de script C#

L’exemple suivant utilise l’identité de l’application pour actualiser un abonnement.

Le fichier *function.json* définit un déclencheur de minuteur avec une liaison d’entrée d’abonnement et une liaison de sortie d’abonnement :

```json
{
  "bindings": [
    {
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in",
      "schedule": "0 * * */2 * *"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "existingSubscriptions",
      "direction": "in"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "subscriptionsToRefresh",
      "direction": "out",
      "action": "refresh",
      "identity": "clientCredentials"
    }
  ],
  "disabled": false
}
```

Le code JavaScript actualise les abonnements :

```js
// This template uses application permissions and requires consent from an Azure Active Directory admin.
// See https://go.microsoft.com/fwlink/?linkid=858780

module.exports = function (context) {
    const existing = context.bindings.existingSubscriptions;
    var toRefresh = [];
    for (var i = 0; i < existing.length; i++) {
        context.log(`Deleting subscription ${existing[i]}`);
        todelete.push(existing[i]);
    }
    context.bindings.subscriptionsToRefresh = toRefresh;
    context.done();
};
```

### <a name="webhook-subscription-refresh---user-identity-example"></a>Actualisation de l’abonnement webhook - exemple d’identité d’utilisateur

L’exemple suivant utilise l’identité de l’utilisateur pour actualiser un abonnement.

Le fichier *function.json* définit un déclencheur de minuteur et confie la liaison d’entrée d’abonnement au code de fonction :

```json
{
  "bindings": [
    {
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in",
      "schedule": "0 * * */2 * *"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "existingSubscriptions",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

Le code de script C# actualise les abonnements et crée la liaison de sortie dans le code à l’aide de l’identité de chaque utilisateur :

```csharp
using System;

public static async Task Run(TimerInfo myTimer, UserSubscription[] existingSubscriptions, IBinder binder, TraceWriter log)
{
  log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
    foreach (var subscription in existingSubscriptions)
    {
        // binding in code to allow dynamic identity
        using (var subscriptionsToRefresh = await binder.BindAsync<IAsyncCollector<string>>(
            new GraphWebhookSubscriptionAttribute() {
                Action = "refresh",
                Identity = "userFromId",
                UserId = subscription.UserId
            }
        ))
        {
            log.Info($"Refreshing subscription {subscription}");
            await subscriptionsToRefresh.AddAsync(subscription);
        }

    }
}

public class UserSubscription {
    public string UserId {get; set;}
    public string Id {get; set;}
}
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [En savoir plus sur les déclencheurs et les liaisons Azure Functions](functions-triggers-bindings.md)

[déclencheur HTTP]: functions-bindings-http-webhook.md
[Utilisation de webhooks dans Microsoft Graph]: https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/webhooks
