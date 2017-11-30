---
title: Liaisons Microsoft Graph dans Azure Functions | Microsoft Docs
description: "Découvrez comment utiliser des déclencheurs et liaisons Microsoft Graph dans Azure Functions."
services: functions
author: mattchenderson
manager: cfowler
editor: 
ms.service: functions
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 09/19/2017
ms.author: mahender
ms.openlocfilehash: dd7bcd57260b9763eabb9b4c915d9ff46e79e931
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/30/2017
---
# <a name="azure-functions-microsoft-graph-bindings"></a>Liaisons Microsoft Graph dans Azure Functions

Cet article explique comment configurer et utiliser des déclencheurs et liaisons Microsoft Graph dans Azure Functions.
Ces fonctions vous permettent d’utiliser Azure Functions pour travailler sur des données, des informations et des événements à partir de [Microsoft Graph](https://graph.microsoft.io).

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
> Les liaisons Microsoft Graph sont actuellement en préversion.

## <a name="setting-up-the-extensions"></a>Configuration des extensions

Les liaisons Microsoft Graph sont disponibles via des _extensions de liaison_. Les extensions de liaison sont des composants facultatifs du runtime Azure Functions. Cette section montre comment configurer l’élément Microsoft Graph et les extensions de jeton d’authentification.

### <a name="enabling-functions-20-preview"></a>Activation de la préversion d’Azure Functions 2.0

Les extensions de liaison sont disponibles uniquement pour la préversion d’Azure Functions 2.0. 

[!INCLUDE [functions-set-runtime-version](../../includes/functions-set-runtime-version.md)]

Pour en savoir plus, voir [Comment cibler des versions du runtime Azure Functions](functions-versions.md).

### <a name="installing-the-extension"></a>Installation de l’extension

Pour installer une extension à partir du portail Azure, vous devez accéder à un modèle ou à une liaison qui y fait référence. Créez une fonction, puis dans l’écran de sélection de modèle, choisissez le scénario de « Microsoft Graph ». Sélectionnez l’un des modèles à partir de ce scénario. Vous pouvez également accéder à l’onglet « Intégrer » d’une fonction existante et sélectionner l’une des liaisons décrites dans cette rubrique.

Dans les deux cas, un avertissement s’affiche, qui spécifie l’extension à installer. Cliquez sur **Installer** pour obtenir de l’extension.

> [!Note] 
> Chaque extension ne doit être installée qu’une seule fois par application de fonction. Le processus d’installation dans le portail peut prendre jusqu'à 10 minutes d’un plan de consommation.

Si vous utilisez Visual Studio, vous pouvez obtenir les extensions en installant les packages NuGet suivants :
- [Microsoft.Azure.WebJobs.Extensions.AuthTokens](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.AuthTokens/)
- [Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph/)

### <a name="configuring-app-service-authentication--authorization"></a>Configuration de l’authentification et des autorisations App Service

Une identité est nécessaire pour pouvoir utiliser les liaisons décrites dans cette rubrique. Cela permet à l’élément Microsoft Graph d’appliquer des autorisations et des interactions d’audit. L’identité peut être un utilisateur accédant à votre application ou à l’application proprement dite. Pour configurer cette identité, vous devez configurer [Authentification et autorisations App Service](https://docs.microsoft.com/azure/app-service/app-service-authentication-overview) avec Azure Active Directory. Vous devez également de demander les autorisations de ressources éventuelles que vos fonctions requièrent.

> [!Note] 
> L’extension Microsoft Graph ne prend en charge que l’authentification AAD. Les utilisateurs doivent se connecter avec un compte professionnel ou scolaire.

Si vous utilisez le portail Azure, sous l’invite à installer l’extension, vous pouvez voir un avertissement vous invitant à configurer l’authentification et les autorisations App Service, ainsi qu’à demander les autorisations que le modèle ou la liaison requièrent. Cliquez sur **Configurer AAD maintenant** ou **Ajouter des autorisations maintenant** selon le cas.






<a name="token-input"></a>
## <a name="auth-token-input-binding"></a>Liaison d’entrée de jeton d’authentification

Cette liaison obtient un jeton AAD pour une ressource donnée, puis fournit celui-ci à votre code en tant que chaîne. La ressource peut être toute ressource pour laquelle l’application dispose d’autorisations. 

### <a name="configuring-an-auth-token-input-binding"></a>Configuration d’une liaison d’entrée de jeton d’authentification

La liaison elle-même ne nécessite pas d’autorisations AAD mais, selon la façon dont le jeton est utilisé, il se peut que vous deviez demander des autorisations supplémentaires. Vérifiez les exigences de la ressource à laquelle vous avez l’intention d’accéder avec le jeton.

La liaison prend en charge les propriétés suivantes :

|Propriété|Description|
|--------|--------|
|**name**|Obligatoire : nom de variable utilisé dans le code de fonction pour le jeton d'authentification. Voir [Utilisation d’une liaison d’entrée de jeton d’authentification à partir du code](#token-input-code).|
|**type**|Obligatoire : doit être défini sur `token`.|
|**direction**|Obligatoire : doit être défini sur `in`.|
|**identity**|Obligatoire : identité utilisée pour effectuer l’action. Peut être l’une des valeurs suivantes :<ul><li><code>userFromRequest</code> : valide uniquement avec [déclencheur HTTP]. Utilise l’identité de l’utilisateur appelant.</li><li><code>userFromId</code> : utilise l’identité d’un utilisateur qui s’est précédemment connecté avec l’ID spécifié. Voir la propriété <code>userId</code>.</li><li><code>userFromToken</code> : utilise l’identité représentée par le jeton spécifié. Voir la propriété <code>userToken</code>.</li><li><code>clientCredentials</code> : utilise l’identité de l’application de fonction.</li></ul>|
|**userId** |Obligatoire si et seulement si la propriété _identity_ a la valeur `userFromId`. ID principal associé à un utilisateur qui s’est précédemment connecté.|
|**userToken**|Obligatoire si et seulement si la propriété _identity_ a la valeur `userFromToken`. Jeton valide pour l’application de fonction. |
|**resource**|Obligatoire : URL de la ressource AAD pour laquelle le jeton est demandé.|

<a name="token-input-code"></a>
### <a name="using-an-auth-token-input-binding-from-code"></a>Utilisation d’une liaison d’entrée de jeton d’authentification à partir du code

Le jeton est toujours présenté au code sous forme de chaîne.

#### <a name="sample-getting-user-profile-information"></a>Exemple : obtention d’informations de profil utilisateur

Supposons que vous ayez le fichier function.json suivant qui définit un déclencheur HTTP avec une liaison d’entrée de jeton :

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

L’exemple de code C# suivant utilise le jeton pour effectuer un appel HTTP à l’élément Microsoft Graph, puis retourne le résultat :

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

L’exemple de code JS suivant utilise le jeton pour effectuer un appel HTTP à l’élément Microsoft Graph, puis retourne le résultat. Dans le fichier `function.json` ci-dessus, commencez par remplacer `$return` par `res`.

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





<a name="excel-input"></a>
## <a name="excel-table-input-binding"></a>Liaison d’entrée de tableau Axcel

Cette liaison lit le contenu d’un tableau Excel stocké sur OneDrive.

### <a name="configuring-an-excel-table-input-binding"></a>Configuration d’une liaison d’entrée de tableau Excel

Cette liaison requiert les autorisations AAD suivantes :
|Ressource|Autorisation|
|--------|--------|
|Microsoft Graph|Lire les fichiers utilisateur|

La liaison prend en charge les propriétés suivantes :

|Propriété|Description|
|--------|--------|
|**name**|Obligatoire : nom de variable utilisé dans le code de fonction pour le tableau Excel. Voir [Utilisation d’une liaison d’entrée de tableau Excel à partir du code](#excel-input-code).|
|**type**|Obligatoire : doit être défini sur `excel`.|
|**direction**|Obligatoire : doit être défini sur `in`.|
|**identity**|Obligatoire : identité utilisée pour effectuer l’action. Peut être l’une des valeurs suivantes :<ul><li><code>userFromRequest</code> : valide uniquement avec [déclencheur HTTP]. Utilise l’identité de l’utilisateur appelant.</li><li><code>userFromId</code> : utilise l’identité d’un utilisateur qui s’est précédemment connecté avec l’ID spécifié. Voir la propriété <code>userId</code>.</li><li><code>userFromToken</code> : utilise l’identité représentée par le jeton spécifié. Voir la propriété <code>userToken</code>.</li><li><code>clientCredentials</code> : utilise l’identité de l’application de fonction.</li></ul>|
|**userId** |Obligatoire si et seulement si la propriété _identity_ a la valeur `userFromId`. ID principal associé à un utilisateur qui s’est précédemment connecté.|
|**userToken**|Obligatoire si et seulement si la propriété _identity_ a la valeur `userFromToken`. Jeton valide pour l’application de fonction. |
|**path**|Obligatoire : chemin d’accès au classeur Excel sur OneDrive.|
|**worksheetName**|La feuille de calcul dans laquelle se trouve le tableau.|
|**tableName**|Nom de la table. Si non spécifié, le contenu de la feuille de calcul est utilisé.|

<a name="excel-input-code"></a>
### <a name="using-an-excel-table-input-binding-from-code"></a>Utilisation d’une liaison d’entrée de tableau Excel à partir du code

La liaison expose les types suivants de fonctions .NET :
- string[][]
- Microsoft.Graph.WorkbookTable
- Types d’objets personnalisés (utilisant une liaison de modèle structurel)

#### <a name="sample-reading-an-excel-table"></a>Exemple : lecture d’un tableau Excel

Supposons que vous ayez le fichier function.json suivant qui définit un déclencheur HTTP avec une liaison d’entrée de tableau Excel :

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

L’exemple de code C# suivant ajoute des lectures du contenu du tableau spécifié, puis les retourne à l’utilisateur :

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives; 

public static IActionResult Run(HttpRequest req, string[][] excelTableData, TraceWriter log)
{
    return new OkObjectResult(excelTableData);
}
```

L’exemple de code JS suivant ajoute des lectures du contenu du tableau spécifié, puis les retourne à l’utilisateur. Dans le fichier `function.json` ci-dessus, commencez par remplacer `$return` par `res`.

```js
module.exports = function (context, req) {
    context.res = {
        body: context.bindings.excelTableData
    };
    context.done();
};
```

<a name="excel-output"></a>
## <a name="excel-table-output-binding"></a>Liaison de sortie de tableau Excel

Cette liaison modifier le contenu d’un tableau Excel stocké sur OneDrive.

### <a name="configuring-an-excel-table-output-binding"></a>Configuration d’une liaison de sortie de tableau Excel

Cette liaison requiert les autorisations AAD suivantes :
|Ressource|Autorisation|
|--------|--------|
|Microsoft Graph|Avoir un accès total aux fichiers utilisateur|

La liaison prend en charge les propriétés suivantes :

|Propriété|Description|
|--------|--------|
|**name**|Obligatoire : nom de variable utilisé dans le code de fonction pour le jeton d'authentification. Voir [Utilisation d’une liaison de sortie de tableau Excel à partir du code](#excel-output-code).|
|**type**|Obligatoire : doit être défini sur `excel`.|
|**direction**|Obligatoire : doit être défini sur `out`.|
|**identity**|Obligatoire : identité utilisée pour effectuer l’action. Peut être l’une des valeurs suivantes :<ul><li><code>userFromRequest</code> : valide uniquement avec [déclencheur HTTP]. Utilise l’identité de l’utilisateur appelant.</li><li><code>userFromId</code> : utilise l’identité d’un utilisateur qui s’est précédemment connecté avec l’ID spécifié. Voir la propriété <code>userId</code>.</li><li><code>userFromToken</code> : utilise l’identité représentée par le jeton spécifié. Voir la propriété <code>userToken</code>.</li><li><code>clientCredentials</code> : utilise l’identité de l’application de fonction.</li></ul>|
|**userId** |Obligatoire si et seulement si la propriété _identity_ a la valeur `userFromId`. ID principal associé à un utilisateur qui s’est précédemment connecté.|
|**userToken**|Obligatoire si et seulement si la propriété _identity_ a la valeur `userFromToken`. Jeton valide pour l’application de fonction. |
|**path**|Obligatoire : chemin d’accès au classeur Excel sur OneDrive.|
|**worksheetName**|La feuille de calcul dans laquelle se trouve le tableau.|
|**tableName**|Nom de la table. Si non spécifié, le contenu de la feuille de calcul est utilisé.|
|**updateType**|Obligatoire : type de modification à apporter au tableau. Peut être l’une des valeurs suivantes :<ul><li><code>update</code> : remplace le contenu du tableau sur OneDrive.</li><li><code>append</code> : ajoute la charge utile à la fin du tableau sur OneDrive en créant des lignes.</li></ul>|

<a name="excel-output-code"></a>
### <a name="using-an-excel-table-output-binding-from-code"></a>Utilisation d’une liaison de sortie de tableau Excel à partir du code

La liaison expose les types suivants de fonctions .NET :
- string[][]
- Newtonsoft.Json.Linq.JObject
- Microsoft.Graph.WorkbookTable
- Types d’objets personnalisés (utilisant une liaison de modèle structurel)

#### <a name="sample-adding-rows-to-an-excel-table"></a>Exemple : ajout de lignes à un tableau Excel

Supposons que vous ayez le fichier function.json suivant qui définit un déclencheur HTTP avec une liaison de sortie de tableau Excel :

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


L’exemple de code C# suivant ajoute une ligne au tableau (supposé ne contenir qu’une seule colonne) basée sur l’entrée de la chaîne de requête :

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

L’exemple de code JS suivant ajoute une ligne au tableau (supposé ne contenir qu’une seule colonne) basée sur l’entrée de la chaîne de requête. Dans le fichier `function.json` ci-dessus, commencez par remplacer `$return` par `res`.

```js
module.exports = function (context, req) {
    context.bindings.newExcelRow = {
        text: req.query.text
        // Add other properties for additional columns here
    }
    context.done();
};
```




<a name="onedrive-input"></a>
## <a name="onedrive-file-input-binding"></a>Liaison d’entrée de fichier OneDrive

Cette liaison lit le contenu d’un fichier stocké sur OneDrive.

### <a name="configuring-a-onedrive-file-input-binding"></a>Configuration d’une liaison d’entrée de fichier OneDrive

Cette liaison requiert les autorisations AAD suivantes :
|Ressource|Autorisation|
|--------|--------|
|Microsoft Graph|Lire les fichiers utilisateur|

La liaison prend en charge les propriétés suivantes :

|Propriété|Description|
|--------|--------|
|**name**|Obligatoire : nom de variable utilisé dans le code de fonction pour le fichier. Voir [Utilisation d’une liaison d’entrée de fichier OneDrive à partir du code](#onedrive-input-code).|
|**type**|Obligatoire : doit être défini sur `onedrive`.|
|**direction**|Obligatoire : doit être défini sur `in`.|
|**identity**|Obligatoire : identité utilisée pour effectuer l’action. Peut être l’une des valeurs suivantes :<ul><li><code>userFromRequest</code> : valide uniquement avec [déclencheur HTTP]. Utilise l’identité de l’utilisateur appelant.</li><li><code>userFromId</code> : utilise l’identité d’un utilisateur qui s’est précédemment connecté avec l’ID spécifié. Voir la propriété <code>userId</code>.</li><li><code>userFromToken</code> : utilise l’identité représentée par le jeton spécifié. Voir la propriété <code>userToken</code>.</li><li><code>clientCredentials</code> : utilise l’identité de l’application de fonction.</li></ul>|
|**userId** |Obligatoire si et seulement si la propriété _identity_ a la valeur `userFromId`. ID principal associé à un utilisateur qui s’est précédemment connecté.|
|**userToken**|Obligatoire si et seulement si la propriété _identity_ a la valeur `userFromToken`. Jeton valide pour l’application de fonction. |
|**path**|Obligatoire : chemin d’accès au fichier sur OneDrive.|

<a name="onedrive-input-code"></a>
### <a name="using-a-onedrive-file-input-binding-from-code"></a>Utilisation d’une liaison d’entrée de fichier OneDrive à partir du code

La liaison expose les types suivants de fonctions .NET :
- byte[]
- Stream
- string
- Microsoft.Graph.DriveItem

#### <a name="sample-reading-a-file-from-onedrive"></a>Exemple : lecture d’un fichier à partir de OneDrive

Supposons que vous ayez le fichier function.json suivant qui définit un déclencheur HTTP avec une liaison d’entrée de fichier OndeDrive :

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

L’exemple de code C# suivant lit le fichier spécifié dans la chaîne de requête et journalise la longueur de celui-ci :

```csharp
using System.Net;

public static void Run(HttpRequestMessage req, Stream myOneDriveFile, TraceWriter log)
{
    log.Info(myOneDriveFile.Length.ToString());
}
```

L’exemple de code JS suivant lit le fichier spécifié dans la chaîne de requête et retourne la longueur de celui-ci. Dans le fichier `function.json` ci-dessus, commencez par remplacer `$return` par `res`.

```js
module.exports = function (context, req) {
    context.res = {
        body: context.bindings.myOneDriveFile.length
    };
    context.done();
};
```


<a name="onedrive-output"></a>
## <a name="onedrive-file-output-binding"></a>Liaison de sortie de fichier OneDrive

Cette liaison modifie le contenu d’un fichier stocké sur OneDrive.

### <a name="configuring-a-onedrive-file-output-binding"></a>Configuration d’une liaison de sortie de fichier OneDrive

Cette liaison requiert les autorisations AAD suivantes :
|Ressource|Autorisation|
|--------|--------|
|Microsoft Graph|Avoir un accès total aux fichiers utilisateur|

La liaison prend en charge les propriétés suivantes :

|Propriété|Description|
|--------|--------|
|**name**|Obligatoire : nom de variable utilisé dans le code de fonction pour le fichier. Voir [Utilisation d’une liaison de sortie de fichier OneDrive à partir du code](#onedrive-output-code).|
|**type**|Obligatoire : doit être défini sur `onedrive`.|
|**direction**|Obligatoire : doit être défini sur `out`.|
|**identity**|Obligatoire : identité utilisée pour effectuer l’action. Peut être l’une des valeurs suivantes :<ul><li><code>userFromRequest</code> : valide uniquement avec [déclencheur HTTP]. Utilise l’identité de l’utilisateur appelant.</li><li><code>userFromId</code> : utilise l’identité d’un utilisateur qui s’est précédemment connecté avec l’ID spécifié. Voir la propriété <code>userId</code>.</li><li><code>userFromToken</code> : utilise l’identité représentée par le jeton spécifié. Voir la propriété <code>userToken</code>.</li><li><code>clientCredentials</code> : utilise l’identité de l’application de fonction.</li></ul>|
|**userId** |Obligatoire si et seulement si la propriété _identity_ a la valeur `userFromId`. ID principal associé à un utilisateur qui s’est précédemment connecté.|
|**userToken**|Obligatoire si et seulement si la propriété _identity_ a la valeur `userFromToken`. Jeton valide pour l’application de fonction. |
|**path**|Obligatoire : chemin d’accès au fichier sur OneDrive.|

<a name="onedrive-output-code"></a>
### <a name="using-a-onedrive-file-output-binding-from-code"></a>Utilisation d’une liaison de sortie de fichier OneDrive à partir du code

La liaison expose les types suivants de fonctions .NET :
- byte[]
- Stream
- string
- Microsoft.Graph.DriveItem

#### <a name="sample-writing-to-a-file-in-onedrive"></a>Exemple : écriture dans un fichier sur OneDrive

Supposons que vous ayez le fichier function.json suivant qui définit un déclencheur HTTP avec une liaison de sortie de fichier OndeDrive :

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

L’exemple de code C# suivant obtient le texte de la chaîne de requête et écrit ce texte dans un fichier texte (FunctionsTest.txt, tel que défini dans la configuration ci-dessus) à la racine du OneDrive de l’appelant :

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
L’exemple de code JS suivant obtient le texte de la chaîne de requête et écrit ce texte dans un fichier texte (FunctionsTest.txt, tel que défini dans la configuration ci-dessus) à la racine du OneDrive de l’appelant. Dans le fichier `function.json` ci-dessus, commencez par remplacer `$return` par `res`.

```js
module.exports = function (context, req) {
    context.bindings.myOneDriveFile = req.query.text;
    context.done();
};
```



<a name="outlook-output"></a>
## <a name="outlook-message-output-binding"></a>Liaison de sortie de message Outlook

Envoie un e-mail via Outlook.

### <a name="configuring-an-outlook-message-output-binding"></a>Configuration d’une liaison de sortie de message Outlook

Cette liaison requiert les autorisations AAD suivantes :
|Ressource|Autorisation|
|--------|--------|
|Microsoft Graph|Envoyer un e-mail en tant qu’utilisateur|

La liaison prend en charge les propriétés suivantes :

|Propriété|Description|
|--------|--------|
|**name**|Obligatoire : nom de variable utilisé dans le code de fonction pour l’e-mail. Voir [Utilisation d’une liaison de sortie de message Outlook à partir du code](#outlook-output-code).|
|**type**|Obligatoire : doit être défini sur `outlook`.|
|**direction**|Obligatoire : doit être défini sur `out`.|
|**identity**|Obligatoire : identité utilisée pour effectuer l’action. Peut être l’une des valeurs suivantes :<ul><li><code>userFromRequest</code> : valide uniquement avec [déclencheur HTTP]. Utilise l’identité de l’utilisateur appelant.</li><li><code>userFromId</code> : utilise l’identité d’un utilisateur qui s’est précédemment connecté avec l’ID spécifié. Voir la propriété <code>userId</code>.</li><li><code>userFromToken</code> : utilise l’identité représentée par le jeton spécifié. Voir la propriété <code>userToken</code>.</li><li><code>clientCredentials</code> : utilise l’identité de l’application de fonction.</li></ul>|
|**userId** |Obligatoire si et seulement si la propriété _identity_ a la valeur `userFromId`. ID principal associé à un utilisateur qui s’est précédemment connecté.|
|**userToken**|Obligatoire si et seulement si la propriété _identity_ a la valeur `userFromToken`. Jeton valide pour l’application de fonction. |

<a name="outlook-output-code"></a>
### <a name="using-an-outlook-message-output-binding-from-code"></a>Utilisation d’une liaison de sortie de message Outlook à partir du code

La liaison expose les types suivants de fonctions .NET :
- Microsoft.Graph.Message
- Newtonsoft.Json.Linq.JObject
- string
- Types d’objets personnalisés (utilisant une liaison de modèle structurel)

#### <a name="sample-sending-an-email-through-outlook"></a>Exemple : envoi d’un e-mail via Outlook

Supposons que vous ayez le fichier function.json suivant qui définit un déclencheur HTTP avec une liaison de sortie de message Outlook :

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

L’exemple de code C# suivant envoie un e-mail de l’appelant à un destinataire spécifié dans la chaîne de requête :

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

L’exemple de code JS suivant envoie un e-mail de l’appelant à un destinataire spécifié dans la chaîne de requête :

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





<a name="webhooks"></a>
## <a name="microsoft-graph-webhook-bindings"></a>Liaisons de webhook Microsoft Graph

Les webhooks vous permettent de réagir à événements dans l’élément Microsoft Graph. Pour prendre en charge les webhooks, des fonctions sont requises pour créer et actualiser les _webhook abonnements_, ainsi qu’y réagir. Une solution webhook complète requiert une combinaison des liaisons suivantes :
- Un [déclencheur de webhook Microsoft Graph](#webhook-trigger) vous permet de réagir à un webhook entrant.
- Une [liaison d’entrée d’abonnement webhook Microsoft Graph](#webhook-input) permet de répertorier les abonnements existants et éventuellement de les actualiser.
- Une [liaison de sortie d’abonnement webhook Microsoft Graph](#webhook-output) permet de créer ou supprimer des abonnements webhook.

Les liaisons proprement dites ne nécessitent pas d’autorisations AAD, mais vous devez demander des autorisations appropriées pour le type de ressource auquel vous souhaitez réagir. Pour la liste des autorisations nécessaires pour chaque type de ressource, voir [Autorisations](https://developer.microsoft.com/en-us/graph/docs/api-reference/v1.0/api/subscription_post_subscriptions#permissions).

Pour plus d’informations sur les webhooks, voir [Utilisation de webhooks dans Microsoft Graph].





<a name="webhook-trigger"></a>
### <a name="microsoft-graph-webhook-trigger"></a>Déclencheur de webhook Microsoft Graph

Ce déclencheur permet à une fonction de réagir à un webhook entrant à partir de l’élément Microsoft Graph. Chaque instance de ce déclencheur peut réagir à un seul type de ressource Microsoft Graph.

#### <a name="configuring-a-microsoft-graph-webhook-trigger"></a>Configuration d’un déclencheur de webhook Microsoft Graph

La liaison prend en charge les propriétés suivantes :

|Propriété|Description|
|--------|--------|
|**name**|Obligatoire : nom de variable utilisé dans le code de fonction pour l’e-mail. Voir [Utilisation d’une liaison de sortie de message Outlook à partir du code](#outlook-output-code).|
|**type**|Obligatoire : doit être défini sur `graphWebhook`.|
|**direction**|Obligatoire : doit être défini sur `trigger`.|
|**resourceType**|Obligatoire : ressource graphique pour laquelle cette fonction doit répondre aux webhooks. Peut être l’une des valeurs suivantes :<ul><li><code>#Microsoft.Graph.Message</code> : modifications apportées aux messages Outlook.</li><li><code>#Microsoft.Graph.DriveItem</code> : modifications apportées aux éléments racine de OneDrive.</li><li><code>#Microsoft.Graph.Contact - changes made to personal contacts in Outlook.</code></li><li><code>#Microsoft.Graph.Event - changes made to Outlook calendar items.</code></li></ul>|

> [!Note]
> Une application de fonction ne peut avoir qu’une seule fonction inscrite par rapport à une donnée `resourceType`.

#### <a name="using-a-microsoft-graph-webhook-trigger-from-code"></a>Utilisation d’un déclencheur de webhook Microsoft Graph à partir du code

La liaison expose les types suivants de fonctions .NET :
- Types de Kits de développement logiciel Microsoft Graph pertinents pour le type de ressource, par exemple, Microsoft.Graph.Message, Microsoft.Graph.DriveItem
- Types d’objets personnalisés (utilisant une liaison de modèle structurel)

Pour des exemples d’utilisation de cette liaison dans le code, voir [Exemples de webhooks Microsoft Graph](#webhook-samples).



<a name="webhook-input"></a>
### <a name="microsoft-graph-webhook-subscription-input-binding"></a>Liaison d’entrée d’abonnement webhook Microsoft Graph

Cette liaison permet de récupérer la liste des abonnements gérés par cette application de fonction. La liaison lit à partir d’un stockage d’applications de fonction, et ne reflète pas d’autres abonnements créés à l’extérieur de l’application.

#### <a name="configuring-a-microsoft-graph-webhook-subscription-input-binding"></a>Configuration d’une liaison d’entrée d’abonnement webhook Microsoft Graph

La liaison prend en charge les propriétés suivantes :

|Propriété|Description|
|--------|--------|
|**name**|Obligatoire : nom de variable utilisé dans le code de fonction pour l’e-mail. Voir [Utilisation d’une liaison de sortie de message Outlook à partir du code](#outlook-output-code).|
|**type**|Obligatoire : doit être défini sur `graphWebhookSubscription`.|
|**direction**|Obligatoire : doit être défini sur `in`.|
|**filter**| Si la valeur est définie sur `userFromRequest`, la liaison extrait uniquement les abonnements appartenant à l’utilisateur appelant (valide uniquement avec un [déclencheur HTTP]).| 

#### <a name="using-a-microsoft-graph-webhook-subscription-input-binding-from-code"></a>Utilisation d’une liaison d’entrée d’abonnement webhook Microsoft Graph à partir du code

La liaison expose les types suivants de fonctions .NET :
- string[]
- Tableaux de types d’objets personnalisés
- Newtonsoft.Json.Linq.JObject[]
- Microsoft.Graph.Subscription[]

Pour des exemples d’utilisation de cette liaison dans le code, voir [Exemples de webhooks Microsoft Graph](#webhook-samples).


<a name="webhook-output"></a>
### <a name="microsoft-graph-webhook-subscription-output-binding"></a>Liaison de sortie abonnement webhook Microsoft Graph

Cette liaison permet de créer, de supprimer et d’actualiser des abonnements webhook dans l’élément Microsoft Graph.

#### <a name="configuring-a-microsoft-graph-webhook-subscription-output-binding"></a>Configuration d’une liaison de sortie d’abonnement webhook Microsoft Graph

La liaison prend en charge les propriétés suivantes :

|Propriété|Description|
|--------|--------|
|**name**|Obligatoire : nom de variable utilisé dans le code de fonction pour l’e-mail. Voir [Utilisation d’une liaison de sortie de message Outlook à partir du code](#outlook-output-code).|
|**type**|Obligatoire : doit être défini sur `graphWebhookSubscription`.|
|**direction**|Obligatoire : doit être défini sur `out`.|
|**identity**|Obligatoire : identité utilisée pour effectuer l’action. Peut être l’une des valeurs suivantes :<ul><li><code>userFromRequest</code> : valide uniquement avec [déclencheur HTTP]. Utilise l’identité de l’utilisateur appelant.</li><li><code>userFromId</code> : utilise l’identité d’un utilisateur qui s’est précédemment connecté avec l’ID spécifié. Voir la propriété <code>userId</code>.</li><li><code>userFromToken</code> : utilise l’identité représentée par le jeton spécifié. Voir la propriété <code>userToken</code>.</li><li><code>clientCredentials</code> : utilise l’identité de l’application de fonction.</li></ul>|
|**userId** |Obligatoire si et seulement si la propriété _identity_ a la valeur `userFromId`. ID principal associé à un utilisateur qui s’est précédemment connecté.|
|**userToken**|Obligatoire si et seulement si la propriété _identity_ a la valeur `userFromToken`. Jeton valide pour l’application de fonction. |
|**action**|Obligatoire : spécifie l’action que la liaison doit effectuer. Peut être l’une des valeurs suivantes :<ul><li><code>create</code> : inscrit un nouvel abonnement.</li><li><code>delete</code> : supprime un abonnement spécifié.</li><li><code>refresh</code> : actualise un abonnement spécifié pour empêcher son expiration.</li></ul>|
|**subscriptionResource**|Obligatoire si et seulement si la propriété _action_ a la valeur `create`. Spécifie la ressource Microsoft Graph dont les modifications doivent être surveillées. Voir [Utilisation de webhooks dans Microsoft Graph]. |
|**changeType**|Obligatoire si et seulement si la propriété _action_ a la valeur `create`. Indique le type de modification de la ressource d’abonnement qui déclenche une notification. Les valeurs prises en charge sont les suivantes : `created`, `updated`, `deleted`. Plusieurs valeurs peuvent être combinées à l’aide d’une liste séparée par des virgules.|

#### <a name="using-a-microsoft-graph-webhook-subscription-output-binding-from-code"></a>Utilisation d’une liaison de sortie d’abonnement webhook Microsoft Graph à partir du code

La liaison expose les types suivants de fonctions .NET :
- string
- Microsoft.Graph.Subscription

Pour des exemples d’utilisation de cette liaison dans le code, voir [Exemples de webhooks Microsoft Graph](#webhook-samples).
 
<a name="webhook-samples"></a>
### <a name="microsoft-graph-webhook-samples"></a>Exemples de webhook Microsoft Graph

#### <a name="sample-creating-a-subscription"></a>Exemple : création d’un abonnement

Supposons que vous ayez le fichier function.json suivant qui définit un déclencheur HTTP avec une liaison de sortie d’abonnement en utilisant l’action create :

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "graphwebhook",
      "name": "clientState",
      "direction": "out",
      "action": "create",
      "listen": "me/mailFolders('Inbox')/messages",
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

L’exemple de code C# suivant inscrit un webhook qui informe cette application de fonction quand l’utilisateur appelant reçoit un message Outlook :

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

L’exemple de code JS suivant inscrit un webhook qui informe cette application de fonction quand l’utilisateur appelant reçoit un message Outlook :

```js
const uuidv4 = require('uuid/v4');

module.exports = function (context, req) {
    context.bindings.clientState = uuidv4();
    context.done();
};
```

#### <a name="sample-handling-notifications"></a>Exemple : traitement des notifications

Supposons que vous ayez le fichier function.json suivant qui définit un déclencheur de webhook Microsoft Graph pour traiter les messages Outlook :

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

L’exemple de code C# suivant réagit aux e-mails entrants et journalise le corps de ceux qui sont envoyés par le destinataire et dont l’objet contient la mention « Azure Functions » :

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

L’exemple de code JS suivant réagit aux e-mails entrants et journalise le corps de ceux qui sont envoyés par le destinataire et dont l’objet contient la mention « Azure Functions » :

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

#### <a name="sample-deleting-subscriptions"></a>Exemple : suppression d’abonnements

Supposons que vous ayez le fichier function.json suivant qui définit un déclencheur HTTP avec une liaison d’entrée d’abonnement et une liaison de sortie d’abonnement en utilisant l’action create :

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

L’exemple de code C# suivant obtient tous les abonnements pour l’utilisateur appelant, puis les supprime :

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

L’exemple de code JS suivant obtient tous les abonnements pour l’utilisateur appelant, puis les supprime :

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

#### <a name="sample-refreshing-subscriptions"></a>Exemple : actualisation d’abonnements

Il existe deux approches de l’actualisation d’abonnements :
- Utiliser l’identité de l’application pour gérer tous les abonnements. Cette opération nécessite le consentement d’un administrateur Azure Active Directory. Elle peut être utilisée par tous les langages qu’Azure Functions prend en charge.
- Utiliser l’identité associée à chaque abonnement en liant manuellement chaque ID utilisateur. Un code personnalisé est nécessaire pour établir la liaison. Cette approche ne peut être utilisée que par des fonctions .NET.

Ces deux options sont expliquées ci-dessous.

**Utilisation de l'identité de l'application**

Supposons que vous ayez le fichier function.json suivant qui définit un déclencheur de minuteur avec une liaison d’entrée d’abonnement et une liaison de sortie d’abonnement en utilisant l’identité de l'application :

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

L’exemple de code C# suivant actualise les abonnements sur un minuteur en utilisant l’identité de l’application :

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

L’exemple de code JS suivant actualise les abonnements sur un minuteur en utilisant l’identité de l’application :

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

**Utilisation d’identités d’utilisateur dynamiques**

Pour l’autre option, supposons que vous avez le fichier function.json suivant qui définit un déclencheur de minuteur, en déférant la liaison à la liaison d’entrée d’abonnement au code de la fonction :

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

L’exemple de code C# suivant actualise les abonnements sur un minuteur à l’aide de l’identité de chaque utilisateur en créant la liaison de sortie dans le code :
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



[déclencheur HTTP]: functions-bindings-http-webhook.md
[Utilisation de webhooks dans Microsoft Graph]: https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/webhooks

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [En savoir plus sur les déclencheurs et les liaisons Azure Functions](functions-triggers-bindings.md)
