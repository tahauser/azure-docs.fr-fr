---
title: Liaisons de stockage Blob Azure Functions
description: "Comprendre comment utiliser les déclencheurs et les liaisons Stockage Blob Azure dans Azure Functions."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: "azure functions, fonctions, traitement des événements, calcul dynamique, architecture serverless"
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/27/2017
ms.author: glenga
ms.openlocfilehash: e0c608fe3a80c9d704774e592a1eba383bbdffe8
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
# <a name="azure-functions-blob-storage-bindings"></a>Liaisons de stockage Blob Azure Functions

Cet article explique comment utiliser des liaisons Stockage Blob Azure dans Azure Functions. Azure Functions prend en charge les liaisons de déclencheur, d’entrée et de sortie pour les objets blob.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE]
> Les [comptes de stockage d’objets blob uniquement](../storage/common/storage-create-storage-account.md#blob-storage-accounts) ne sont pas pris en charge. Les déclencheurs et les liaisons de stockage blob nécessitent un compte de stockage à usage général. 

## <a name="blob-storage-trigger"></a>Déclencheur de stockage d’objets blob

Utilisez un déclencheur de stockage d’objets blob pour démarrer une fonction lors de la détection d’un objet blob nouveau ou mis à jour. Le contenu de l’objet blob est fourni comme entrée de la fonction.

> [!NOTE]
> Quand vous utilisez un déclencheur d’objet blob dans un plan Consommation, il peut y avoir jusqu’à 10 minutes de délai dans le traitement des nouveaux objets blob après qu’une application de fonction est devenue inactive. Une fois l’application de fonction en cours d’exécution, les objets blob sont traités immédiatement. Pour éviter ce délai initial, pensez à l’une des options suivantes :
> - Utilisez un plan App Service avec le paramètre Toujours actif activé.
> - Utilisez un autre mécanisme pour déclencher le traitement de l’objet blob, comme un message de file d’attente qui contient le nom de l’objet blob. Pour voir un exemple, consultez l’[exemple de liaisons d’entrée/sortie d’objets blob plus loin dans cet article](#input--output---example).

## <a name="trigger---example"></a>Déclencheur - exemple

Consultez l’exemple propre à un langage particulier :

* [C# précompilé](#trigger---c-example)
* [Script C#](#trigger---c-script-example)
* [JavaScript](#trigger---javascript-example)

### <a name="trigger---c-example"></a>Déclencheur - exemple C#

L’exemple suivant montre un code [C# précompilé](functions-dotnet-class-library.md) qui écrit un journal lorsqu’un objet blob est ajouté ou mis à jour dans le conteneur `samples-workitems`.

```csharp
[FunctionName("BlobTriggerCSharp")]        
public static void Run([BlobTrigger("samples-workitems/{name}")] Stream myBlob, string name, TraceWriter log)
{
    log.Info($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

Pour plus d’informations sur l’attribut `BlobTrigger`, consultez [Déclencheur - Attributs pour C# précompilé](#trigger---attributes-for-precompiled-c).

### <a name="trigger---c-script-example"></a>Déclencheur - exemple Script C#

L’exemple suivant montre une liaison de déclencheur d’objet blob dans un fichier *function.json* et dans un code [Script C#](functions-reference-csharp.md) qui utilise la liaison. La fonction écrit un journal lorsqu’un objet blob est ajouté ou mis à jour dans le conteneur `samples-workitems`.

Voici les données de liaison dans le fichier *function.json* :

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

La section [configuration](#trigger---configuration) décrit ces propriétés.

Voici le code Script C# qui lie à `Stream` :

```cs
public static void Run(Stream myBlob, TraceWriter log)
{
   log.Info($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

Voici le code Script C# qui lie à `CloudBlockBlob` :

```cs
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.WindowsAzure.Storage.Blob;

public static void Run(CloudBlockBlob myBlob, string name, TraceWriter log)
{
    log.Info($"C# Blob trigger function Processed blob\n Name:{name}\nURI:{myBlob.StorageUri}");
}
```

### <a name="trigger---javascript-example"></a>Déclencheur - exemple JavaScript

L’exemple suivant montre une liaison de déclencheur d’objet blob dans un fichier *function.json* et dans un [code JavaScript] (functions-reference-node.md) qui utilise la liaison. La fonction écrit un journal lorsqu’un objet blob est ajouté ou mis à jour dans le conteneur `samples-workitems`.

Voici le fichier *function.json* :

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

La section [configuration](#trigger---configuration) décrit ces propriétés.

Voici le code Script C# :

```javascript
module.exports = function(context) {
    context.log('Node.js Blob trigger function processed', context.bindings.myBlob);
    context.done();
};
```

## <a name="trigger---attributes-for-precompiled-c"></a>Déclencheur - Attributs pour C# précompilé

Pour les fonctions en [C# précompilé](functions-dotnet-class-library.md), utilisez les attributs suivants pour configurer un déclencheur d’objet blob :

* [BlobTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobTriggerAttribute.cs), défini dans le package NuGet [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs)

  Le constructeur de l’attribut prend une chaîne de chemin qui indique le conteneur à surveiller et éventuellement un [modèle de nom d’objet blob](#trigger---blob-name-patterns). Voici un exemple :

  ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}")] Stream image, 
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  ```

  Vous pouvez définir la propriété `Connection` pour spécifier le compte de stockage à utiliser, comme indiqué dans l’exemple suivant :

   ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}", Connection = "StorageConnectionAppSetting")] Stream image, 
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  ```

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs), défini dans le package NuGet [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs)

  Fournit une autre manière de spécifier le compte de stockage à utiliser. Le constructeur prend le nom d’un paramètre d’application comportant une chaîne de connexion de stockage. L’attribut peut être appliqué au niveau du paramètre, de la méthode ou de la classe. L’exemple suivant montre le niveau de la classe et celui de la méthode :

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("BlobTrigger")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  ```

Le compte de stockage à utiliser est déterminé dans l’ordre suivant :

* La propriété `Connection` de l’attribut `BlobTrigger`.
* L’attribut `StorageAccount` appliqué au même paramètre que l’attribut `BlobTrigger`.
* L’attribut `StorageAccount` appliqué à la fonction.
* L’attribut `StorageAccount` appliqué à la classe.
* Le compte de stockage par défaut pour l’application de fonction (paramètre d’application « AzureWebJobsStorage »).

## <a name="trigger---configuration"></a>Déclencheur - configuration

Le tableau suivant décrit les propriétés de configuration de liaison que vous définissez dans le fichier *function.json* et l’attribut `BlobTrigger`.

|Propriété function.json | Propriété d’attribut |Description|
|---------|---------|----------------------|
|**type** | n/a | Doit être défini sur `blobTrigger`. Cette propriété est définie automatiquement lorsque vous créez le déclencheur dans le portail Azure.|
|**direction** | n/a | Doit être défini sur `in`. Cette propriété est définie automatiquement lorsque vous créez le déclencheur dans le portail Azure. Les exceptions sont notées à la section [utilisation](#trigger---usage). |
|**name** | n/a | Nom de la variable qui représente l’objet blob dans le code de la fonction. | 
|**path** | **BlobPath** |Conteneur à surveiller.  Peut être un [modèle de nom d’objet blob](#trigger-blob-name-patterns). | 
|**connection** | **Connection** | Nom d’un paramètre d’application comportant la chaîne de connexion de stockage à utiliser pour cette liaison. Si le nom du paramètre d’application commence par « AzureWebJobs », vous ne pouvez spécifier que le reste du nom ici. Par exemple, si vous définissez `connection` sur « MyStorage », le runtime Functions recherche un paramètre d’application qui est nommé « AzureWebJobsMyStorage ». Si vous laissez `connection` vide, le runtime Functions utilise la chaîne de connexion de stockage par défaut dans le paramètre d’application nommé `AzureWebJobsStorage`.<br><br>La chaîne de connexion doit être pour un compte de stockage à usage général, et non pour un [compte de stockage d’objets blob uniquement](../storage/common/storage-create-storage-account.md#blob-storage-accounts).<br>Lorsque vous développez localement, les paramètres d’application passent dans les valeurs du [fichier local.settings.json](functions-run-local.md#local-settings-file).|

## <a name="trigger---usage"></a>Déclencheur - utilisation

Dans C# et Script C#, accédez aux données d’objets blob en utilisant un paramètre de méthode comme `Stream paramName`. Dans Script C#, `paramName` est la valeur spécifiée dans la propriété `name` de *function.json*. Vous pouvez lier aux types suivants :

* `TextReader`
* `Stream`
* `ICloudBlob` (nécessite le sens de la liaison « inout » dans *function.json*)
* `CloudBlockBlob` (nécessite le sens de la liaison « inout » dans *function.json*)
* `CloudPageBlob` (nécessite le sens de la liaison « inout » dans *function.json*)
* `CloudAppendBlob` (nécessite le sens de la liaison « inout » dans *function.json*)

Comme indiqué, certains de ces types nécessitent un sens de liaison `inout` dans *function.json*. Ce sens n’est pas pris en charge par l’éditeur standard du portail Azure : vous devez donc utiliser l’éditeur avancé.

Si des objets blob de texte sont attendus, vous pouvez lier au type `string`. Ceci est recommandé uniquement si la taille de l’objet blob est petite, car tout le contenu de l’objet blob est chargé en mémoire. En général, il est préférable d’utiliser un type `Stream` ou `CloudBlockBlob`.

Dans JavaScript, accédez aux données de l’objet blob d’entrée en utilisant `context.bindings.<name>`.

## <a name="trigger---blob-name-patterns"></a>Déclencheur - modèles de nom d’objet blob

Vous pouvez spécifier un modèle de nom d’objet blob dans la propriété `path` du fichier *function.json* ou dans le constructeur d’attribut `BlobTrigger`. Le modèle de nom peut être une [expression de filtre ou de liaison](functions-triggers-bindings.md#binding-expressions-and-patterns).

### <a name="filter-on-blob-name"></a>Filtrer sur le nom de l’objet blob

L’exemple suivant déclenche uniquement sur les objets blob du conteneur `input` qui commencent par la chaîne « original- » :

```json
"path": "input/original-{name}",
```
 
Si le nom de l’objet blob est *original-Blob1.txt*, la valeur de la variable `name` dans le code de la fonction est `Blob1`.

### <a name="filter-on-file-type"></a>Filtrer sur le type de fichier

L’exemple suivant ne déclenche que sur des fichiers *.png* :

```json
"path": "samples/{name}.png",
```

### <a name="filter-on-curly-braces-in-file-names"></a>Filtrer sur les accolades dans les noms de fichiers

Pour rechercher les accolades dans les noms de fichiers, utilisez une séquence d’échappement sous la forme de deux accolades. L’exemple suivant filtre en recherchant les objets blob qui contiennent des accolades dans le nom :

```json
"path": "images/{{20140101}}-{name}",
```

Si l’objet blob est nommé *{20140101}-soundfile.mp3*, la valeur de la variable `name` dans le code de la fonction est *soundfile.mp3*. 

### <a name="get-file-name-and-extension"></a>Obtenir l’extension et le nom de fichier

L’exemple suivant montre comment se lier séparément à l’extension et au nom de fichier de l’objet blob :

```json
"path": "input/{blobname}.{blobextension}",
```
Si l’objet blob est nommé *original-Blob1.txt*, les valeurs des variables `blobname` et `blobextension` dans le code de la fonction sont *original-Blob1* et *txt*.

## <a name="trigger---metadata"></a>Déclencheur - métadonnées

Le déclencheur d’objet blob fournit plusieurs propriétés de métadonnées. Ces propriétés peuvent être utilisées dans les expressions de liaison dans d’autres liaisons ou en tant que paramètres dans votre code. Ces valeurs ont la même sémantique que le type [CloudBlob](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob?view=azure-dotnet).


|Propriété  |Type  |Description  |
|---------|---------|---------|
|`BlobTrigger`|`string`|Chemin de l’objet blob déclencheur.|
|`Uri`|`System.Uri`|URI de l’objet blob pour l’emplacement principal.|
|`Properties` |[BlobProperties](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.blobproperties)|Propriétés système de l’objet blob. |
|`Metadata` |`IDictionary<string,string>`|Métadonnées définies par l’utilisateur pour l’objet blob.|

## <a name="trigger---blob-receipts"></a>Déclencheur - reçus d’objet blob

Le runtime Azure Functions vérifie qu’aucune fonction de déclencheur d’objet blob n’est appelée plusieurs fois pour un même objet blob, nouveau ou mis à jour. Pour déterminer si la version d’un objet blob donné a été traitée, il gère des *reçus d’objet blob*.

Azure Functions stocke les reçus d’objet blob dans un conteneur appelé *azure-webjobs-hosts* dans le compte de stockage Azure de votre application de fonction (définie par le paramètre d’application `AzureWebJobsStorage`). Un reçu d’objet blob contient les informations suivantes :

* Fonction déclenchée (« *&lt;nom de l’application de fonction>*.Functions.*&lt;nom de la fonction>* », par exemple : « MyFunctionApp.Functions.CopyBlob »)
* Nom du conteneur
* Type d’objet blob (« BlockBlob » ou « PageBlob »)
* Nom de l’objet blob
* ETag (identificateur de version de l’objet blob, par exemple : « 0x8D1DC6E70A277EF »)

Pour forcer le retraitement d’un objet blob, supprimez manuellement le reçu de l’objet blob du conteneur *azure-webjobs-hosts*.

## <a name="trigger---poison-blobs"></a>Déclencheur - objets blob incohérents

En cas d’échec d’une fonction de déclencheur d’objet blob, Azure Functions réessaie cette fonction jusqu’à 5 fois par défaut. 

Si les 5 tentatives échouent, Azure Functions ajoute un message à une file d’attente de stockage nommée *webjobs-blobtrigger-poison*. Le message en file d’attente associé aux objets blob incohérents correspond à un objet JSON, qui contient les propriétés suivantes :

* FunctionId (au format *&lt;nom de l’application de fonction>*.Functions.*&lt;nom de la fonction>*)
* BlobType (« BlockBlob » ou « PageBlob »)
* ContainerName
* BlobName
* ETag (identificateur de version de l’objet blob, par exemple : « 0x8D1DC6E70A277EF »)

## <a name="trigger---polling-for-large-containers"></a>Déclencheur - interrogation pour les grands conteneurs

Si le conteneur d’objets blob surveillé contient plus de 10 000 objets blob, le runtime Functions recherche les objets blob nouveaux ou modifiés dans les fichiers journaux. Ce processus peut entraîner des retards. Il se peut qu’une fonction ne se déclenche que quelques minutes ou plus après la création de l’objet blob. En outre, les [journaux de stockage sont créés selon le principe du meilleur effort](/rest/api/storageservices/About-Storage-Analytics-Logging). Il n’existe aucune garantie que tous les événements sont capturés. Dans certaines conditions, des journaux peuvent être omis. Si vous avez besoin de traitement d’objets blob plus rapide ou plus fiable, envisagez de créer un [message de file d’attente](../storage/queues/storage-dotnet-how-to-use-queues.md) quand vous créez l’objet blob. Ensuite, utilisez un [déclencheur de file d’attente](functions-bindings-storage-queue.md) plutôt qu’un déclencheur d’objet blob pour traiter l’objet blob. Une autre option consiste à utiliser Event Grid ; consultez le didacticiel [Automatiser le redimensionnement des images téléchargées à l’aide d’Event Grid](../event-grid/resize-images-on-storage-blob-upload-event.md).

## <a name="blob-storage-input--output-bindings"></a>Liaisons d’entrée et de sortie du stockage d’objets blob

Utilisez les liaisons d’entrée et de sortie du stockage d’objets blob pour lire et écrire des objets blob.

## <a name="input--output---example"></a>Entrée et sortie - exemple

Consultez l’exemple propre à un langage particulier :

* [C# précompilé](#input--output---c-example)
* [Script C#](#input--output---c-script-example)
* [JavaScript](#input--output---javascript-example)

### <a name="input--output---c-example"></a>Entrée et sortie - exemple C#

L’exemple suivant est une fonction [C# précompilé](functions-dotnet-class-library.md) qui utilise trois liaisons d’objet blob, une d’entrée et deux de sortie. La fonction est déclenchée par la création d’un objet blob d’image dans le conteneur *sample-images*. Il crée des copies de petite et moyenne taille de l’objet blob d’image. 

```csharp
[FunctionName("ResizeImage")]
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

### <a name="input--output---c-script-example"></a>Entrée et sortie - exemple Script C#

L’exemple suivant montre une liaison de déclencheur d’objet blob dans un fichier *function.json* et dans un code [Script C#](functions-reference-csharp.md) qui utilise la liaison. La fonction effectue une copie d’un objet blob. La fonction est déclenchée par un message de file d’attente qui contient le nom de l’objet blob à copier. Le nouvel objet blob est nommé *{originalblobname}-Copy*.

Dans le fichier *function.json*, la propriété de métadonnées `queueTrigger` est utilisée pour spécifier le nom de l’objet blob dans les propriétés `path` :

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

La section [configuration](#input--output---configuration) décrit ces propriétés.

Voici le code Script C# :

```cs
public static void Run(string myQueueItem, Stream myInputBlob, out string myOutputBlob, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

### <a name="input--output---javascript-example"></a>Entrée et sortie - exemple JavaScript

L’exemple suivant montre une liaison de déclencheur d’objet blob dans un fichier *function.json* et dans un [code JavaScript] (functions-reference-node.md) qui utilise la liaison. La fonction effectue une copie d’un objet blob. La fonction est déclenchée par un message de file d’attente qui contient le nom de l’objet blob à copier. Le nouvel objet blob est nommé *{originalblobname}-Copy*.

Dans le fichier *function.json*, la propriété de métadonnées `queueTrigger` est utilisée pour spécifier le nom de l’objet blob dans les propriétés `path` :

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

La section [configuration](#input--output---configuration) décrit ces propriétés.

Voici le code Script C# :

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

## <a name="input--output---attributes-for-precompiled-c"></a>Entrée et sortie - Attributs pour C# précompilé

Pour les fonctions en [C# précompilé](functions-dotnet-class-library.md), utilisez le [BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs) qui est défini dans le package NuGet [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs).

Le constructeur de l’attribut prend le chemin de l’objet blob et un paramètre `FileAccess` indiquant la lecture ou l’écriture, comme indiqué dans l’exemple suivant :

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image, 
    [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
```

Vous pouvez définir la propriété `Connection` pour spécifier le compte de stockage à utiliser, comme indiqué dans l’exemple suivant :

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image, 
    [Blob("sample-images-md/{name}", FileAccess.Write, Connection = "StorageConnectionAppSetting")] Stream imageSmall)
```

Vous pouvez utiliser l’attribut `StorageAccount` pour spécifier le compte de stockage au niveau de la classe, de la méthode ou du paramètre. Pour plus d’informations, consultez [Déclencheur - Attributs pour C# précompilé](#trigger---attributes-for-precompiled-c).

## <a name="input--output---configuration"></a>Entrée et sortie - configuration

Le tableau suivant décrit les propriétés de configuration de liaison que vous définissez dans le fichier *function.json* et l’attribut `Blob`.

|Propriété function.json | Propriété d’attribut |Description|
|---------|---------|----------------------|
|**type** | n/a | Doit être défini sur `blob`. |
|**direction** | n/a | Doit avoir la valeur `in` pour une liaison d’entrée ou out pour une liaison de sortie. Les exceptions sont répertoriées à la section [utilisation](#input--output---usage). |
|**name** | n/a | Nom de la variable qui représente l’objet blob dans le code de la fonction.  La valeur doit être `$return` pour faire référence à la valeur de retour de la fonction.|
|**path** |**BlobPath** | Chemin de l’objet blob. | 
|**connection** |**Connection**| Nom d’un paramètre d’application comportant la chaîne de connexion de stockage à utiliser pour cette liaison. Si le nom du paramètre d’application commence par « AzureWebJobs », vous ne pouvez spécifier que le reste du nom ici. Par exemple, si vous définissez `connection` sur « MyStorage », le runtime Functions recherche un paramètre d’application qui est nommé « AzureWebJobsMyStorage ». Si vous laissez `connection` vide, le runtime Functions utilise la chaîne de connexion de stockage par défaut dans le paramètre d’application nommé `AzureWebJobsStorage`.<br><br>La chaîne de connexion doit être pour un compte de stockage à usage général, et non pour un [compte de stockage d’objets blob uniquement](../storage/common/storage-create-storage-account.md#blob-storage-accounts).<br>Lorsque vous développez localement, les paramètres d’application passent dans les valeurs du [fichier local.settings.json](functions-run-local.md#local-settings-file).|
|n/a | **Access** | Indique si vous lirez ou écrirez. |

## <a name="input--output---usage"></a>Entrée et sortie - utilisation

Dans C# précompilé et Script C#, accédez à l’objet blob à l’aide d’un paramètre de méthode comme `Stream paramName`. Dans Script C#, `paramName` est la valeur spécifiée dans la propriété `name` de *function.json*. Vous pouvez lier aux types suivants :

* `out string`
* `TextWriter` 
* `TextReader`
* `Stream`
* `ICloudBlob` (nécessite le sens de la liaison « inout » dans *function.json*)
* `CloudBlockBlob` (nécessite le sens de la liaison « inout » dans *function.json*)
* `CloudPageBlob` (nécessite le sens de la liaison « inout » dans *function.json*)
* `CloudAppendBlob` (nécessite le sens de la liaison « inout » dans *function.json*)

Comme indiqué, certains de ces types nécessitent un sens de liaison `inout` dans *function.json*. Ce sens n’est pas pris en charge par l’éditeur standard du portail Azure : vous devez donc utiliser l’éditeur avancé.

Si vous lisez des objets blob de texte, vous pouvez lier à un type `string`. Ce type est recommandé uniquement si la taille de l’objet blob est petite, car tout le contenu de l’objet blob est chargé en mémoire. En général, il est préférable d’utiliser un type `Stream` ou `CloudBlockBlob`.

Dans JavaScript, accédez aux données de l’objet blob en utilisant `context.bindings.<name>`.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Accéder à un guide de démarrage rapide qui utilise un déclencheur de stockage d’objets blob](functions-create-storage-blob-triggered-function.md)

> [!div class="nextstepaction"]
> [En savoir plus sur les déclencheurs et les liaisons Azure Functions](functions-triggers-bindings.md)
