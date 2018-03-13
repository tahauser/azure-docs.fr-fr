---
title: "Liaisons de fichiers externes pour Azure Functions (expérimental)"
description: Utilisation de liaisons du fichiers externes dans Azure Functions
services: functions
documentationcenter: 
author: alexkarcher-msft
manager: cfowler
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 11/27/2017
ms.author: alkarche
ms.openlocfilehash: 4e9c2c336df465d7488de84bd2a02cc5d9e42f30
ms.sourcegitcommit: d6984ef8cc057423ff81efb4645af9d0b902f843
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/05/2018
---
# <a name="azure-functions-external-file-bindings-experimental"></a>Liaisons de fichiers externes Azure Functions (expérimental)
Cet article explique comment gérer des fichiers à partir de différents fournisseurs SaaS (par exemple, Dropbox ou Google Drive) dans Azure Functions. Azure Functions prend en charge les liaisons de déclencheur, d’entrée et de sortie pour les fichiers externes. Ces liaisons créent des connexions d’API aux fournisseurs SaaS, ou utilisent des connexions d’API existantes à partir du groupe de ressources de votre application Function App.

> [!IMPORTANT]
> Les liaisons de fichiers externes sont expérimentales et pourraient ne jamais atteindre l’état de disponibilité générale (GA). Elles sont uniquement incluses dans Azure Functions 1.x, et nous ne prévoyons actuellement pas de les ajouter à Azure Functions 2.x. Pour les scénarios qui nécessitent l’accès aux données dans les fournisseurs SaaS, envisagez d’utiliser des [applications logiques qui appellent des fonctions](functions-twitter-email.md). Reportez-vous au [connecteur du système de fichiers d’applications logiques](../logic-apps/logic-apps-using-file-connector.md).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="available-file-connections"></a>Connexions aux fichiers disponibles

|Connecteur|Déclencheur|Entrée|Sortie|
|:-----|:---:|:---:|:---:|
|[Box](https://www.box.com)|x|x|x
|[Dropbox](https://www.dropbox.com)|x|x|x
|[FTP](https://docs.microsoft.com/azure/app-service/app-service-deploy-ftp)|x|x|x
|[OneDrive](https://onedrive.live.com)|x|x|x
|[OneDrive Entreprise](https://onedrive.live.com/about/business/)|x|x|x
|[SFTP](https://docs.microsoft.com/azure/connectors/connectors-create-api-sftp)|x|x|x
|[Google Drive](https://www.google.com/drive/)||x|x|

> [!NOTE]
> Les connexions aux fichiers externes peuvent également servir dans les applications [Azure Logic Apps](https://docs.microsoft.com/azure/connectors/apis-list).

## <a name="trigger"></a>Déclencheur

Le déclencheur de fichier externe vous permet de surveiller un dossier à distance et d’exécuter votre code de fonction en cas de détection de modifications.

## <a name="trigger---example"></a>Déclencheur - exemple

Consultez l’exemple propre à un langage particulier :

* [Script C#](#trigger---c-script-example)
* [JavaScript](#trigger---javascript-example)

### <a name="trigger---c-script-example"></a>Déclencheur - exemple Script C#

L’exemple suivant illustre une liaison de déclencheur de fichier externe dans un fichier *function.json* et une [fonction de script C#](functions-reference-csharp.md) qui utilise la liaison. La fonction enregistre le contenu de chaque fichier ajouté au dossier surveillé.

Voici les données de liaison dans le fichier *function.json* :

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myFile",
            "type": "apiHubFileTrigger",
            "direction": "in",
            "path": "samples-workitems",
            "connection": "<name of external file connection>"
        }
    ]
}
```

Voici le code Script C# :

```cs
public static void Run(string myFile, TraceWriter log)
{
    log.Info($"C# File trigger function processed: {myFile}");
}
```

### <a name="trigger---javascript-example"></a>Déclencheur - exemple JavaScript

L’exemple suivant illustre une liaison de déclencheur de fichiers externes dans un fichier *function.json* et une [fonction JavaScript](functions-reference-node.md) qui utilise la liaison. La fonction enregistre le contenu de chaque fichier ajouté au dossier surveillé.

Voici les données de liaison dans le fichier *function.json* :

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myFile",
            "type": "apiHubFileTrigger",
            "direction": "in",
            "path": "samples-workitems",
            "connection": "<name of external file connection>"
        }
    ]
}
```

Voici le code JavaScript :

```javascript
module.exports = function(context) {
    context.log('Node.js File trigger function processed', context.bindings.myFile);
    context.done();
};
```

## <a name="trigger---configuration"></a>Déclencheur - configuration

Le tableau suivant décrit les propriétés de configuration de liaison que vous définissez dans le fichier *function.json*.

|Propriété function.json | Description|
|---------|---------|----------------------|
|**type** | Cette propriété doit être définie sur `apiHubFileTrigger`. Cette propriété est définie automatiquement lorsque vous créez le déclencheur dans le portail Azure.|
|**direction** | Cette propriété doit être définie sur `in`. Cette propriété est définie automatiquement lorsque vous créez le déclencheur dans le portail Azure. |
|**name** | Nom de la variable qui représente l’élément d’événement dans le code de la fonction. | 
|**Connexion**| Identifie le paramètre d’application qui stocke la chaîne de connexion. Le paramètre d’application est automatiquement créé quand vous ajoutez une connexion dans l’interface utilisateur d’intégration dans le portail Azure.|
|**path** | Dossier à surveiller, et éventuellement un modèle de nom.|

### <a name="name-patterns"></a>Modèles de nom

Vous pouvez spécifier un modèle de nom de fichier dans la propriété `path` . Le dossier référencé doit exister dans le fournisseur SaaS.

Exemples :

```json
"path": "input/original-{name}",
```

Ce chemin d’accès trouverait un fichier appelé *original-File1.txt* dans le dossier *input*, et la variable `name` du code de fonction présenterait la valeur `File1.txt`.

Autre exemple :

```json
"path": "input/{filename}.{fileextension}",
```

Ce chemin d’accès trouverait également un fichier nommé *original-File1.txt*, et les variables `filename` et `fileextension` du code de fonction présenteraient respectivement les valeurs *original-File1* et *txt*.

Vous pouvez restreindre le type des fichiers à l’aide d’une valeur fixe pour l’extension de fichier. Par exemple : 

```json
"path": "samples/{name}.png",
```

Dans ce cas, seuls les fichiers *.png* dans le dossier *samples* déclenchent la fonction.

Les accolades sont des caractères spéciaux dans les modèles de nom. Pour spécifier des noms de fichier qui présentent des accolades, doublez ces dernières.
Par exemple : 

```json
"path": "images/{{20140101}}-{name}",
```

Ce chemin trouverait un fichier nommé *{20140101}-soundfile.mp3* dans le dossier *images*, et la valeur de la variable `name` dans le code de fonction serait *soundfile.mp3*.

## <a name="trigger---usage"></a>Déclencheur - utilisation

Dans les fonctions C#, vous liez les données de fichier d’entrée à l’aide d’un paramètre nommé dans la signature de la fonction, comme `<T> <name>`.
Où `T` est le type de données dans lequel vous souhaitez désérialiser les données, et `paramName` le nom que vous avez spécifié dans le [code JSON du déclencheur](#trigger). Dans les fonctions Node.js, vous accédez aux données de fichier d’entrée en utilisant `context.bindings.<name>`.

Le fichier peut être désérialisé dans l’un des types suivants :

* N’importe quel [objet](https://msdn.microsoft.com/library/system.object.aspx) : utile pour les données de fichier sérialisées en JSON.
  Si vous déclarez un type d’entrée personnalisé (par exemple, `FooType`), Azure Functions tente de désérialiser les données JSON dans le type spécifié.
* Chaîne : utile pour les données de fichier texte.

Dans les fonctions C#, vous pouvez également effectuer une liaison vers un des types suivants (le runtime Functions tente de désérialiser les données du fichier à l’aide de ce type) :

* `string`
* `byte[]`
* `Stream`
* `StreamReader`
* `TextReader`

<!--- ## Trigger - file receipts
The Azure Functions runtime makes sure that no external file trigger function gets called more than once for the same new or updated file.
It does so by maintaining *file receipts* to determine if a given file version has been processed.

File receipts are stored in a folder named *azure-webjobs-hosts* in the Azure storage account for your function app
(specified by the `AzureWebJobsStorage` app setting). A file receipt has the following information:

* The triggered function ("*&lt;function app name>*.Functions.*&lt;function name>*", for example: "functionsf74b96f7.Functions.CopyFile")
* The folder name
* The file type ("BlockFile" or "PageFile")
* The file name
* The ETag (a file version identifier, for example: "0x8D1DC6E70A277EF")

To force reprocessing of a file, delete the file receipt for that file from the *azure-webjobs-hosts* folder manually.
--->

## <a name="trigger---poison-files"></a>Déclencheur - fichiers incohérents

En cas d’échec d’une fonction de déclenchement de fichier externe, Azure Functions réessaie cette fonction jusqu’à 5 fois par défaut (première tentative comprise) pour un fichier donné.
Si toutes les 5 tentatives échouent, Functions ajoute un message à une file d’attente Stockage nommée *webjobs-apihubtrigger-poison*. Le message en file d’attente associé aux fichiers incohérents correspond à un objet JSON, qui contient les propriétés suivantes :

* FunctionId (au format *&lt;nom de l’application de fonctions>*.Functions.*&lt;nom de la fonction>*)
* FileType
* FolderName
* FileName
* ETag (identificateur de version du fichier, par exemple : « 0x8D1DC6E70A277EF »)

## <a name="input"></a>Entrée

La liaison d’entrée de fichier externe Azure vous permet d’utiliser un fichier à partir d’un dossier externe dans votre fonction.

## <a name="input---example"></a>Entrée - exemple

Consultez l’exemple propre à un langage particulier :

* [Script C#](#input---c-script-example)
* [JavaScript](#input---javascript-example)

### <a name="input---c-script-example"></a>Entrée - exemple de script C#

L’exemple suivant montre des liaisons d’entrée et de sortie de fichiers externes dans un fichier *function.json* et une [fonction de script C#](functions-reference-csharp.md) qui utilise la liaison. La fonction copie un fichier d’entrée dans un fichier de sortie.

Voici les données de liaison dans le fichier *function.json* :

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputFile",
      "type": "apiHubFile",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "<name of external file connection>",
      "direction": "in"
    },
    {
      "name": "myOutputFile",
      "type": "apiHubFile",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "<name of external file connection>",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Voici le code Script C# :

```cs
public static void Run(string myQueueItem, string myInputFile, out string myOutputFile, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    myOutputFile = myInputFile;
}
```

### <a name="input---javascript-example"></a>Entrée - exemple JavaScript

L’exemple suivant montre des liaisons d’entrée et de sortie de fichiers externes dans un fichier *function.json* et une [fonction JavaScript](functions-reference-node.md) qui utilise la liaison. La fonction copie un fichier d’entrée dans un fichier de sortie.

Voici les données de liaison dans le fichier *function.json* :

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputFile",
      "type": "apiHubFile",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "<name of external file connection>",
      "direction": "in"
    },
    {
      "name": "myOutputFile",
      "type": "apiHubFile",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "<name of external file connection>",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Voici le code JavaScript :

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputFile = context.bindings.myInputFile;
    context.done();
};
```

## <a name="input---configuration"></a>Entrée - configuration

Le tableau suivant décrit les propriétés de configuration de liaison que vous définissez dans le fichier *function.json*.

|Propriété function.json | Description|
|---------|---------|----------------------|
|**type** | Cette propriété doit être définie sur `apiHubFile`. Cette propriété est définie automatiquement lorsque vous créez le déclencheur dans le portail Azure.|
|**direction** | Cette propriété doit être définie sur `in`. Cette propriété est définie automatiquement lorsque vous créez le déclencheur dans le portail Azure. |
|**name** | Nom de la variable qui représente l’élément d’événement dans le code de la fonction. | 
|**Connexion**| Identifie le paramètre d’application qui stocke la chaîne de connexion. Le paramètre d’application est automatiquement créé quand vous ajoutez une connexion dans l’interface utilisateur d’intégration dans le portail Azure.|
|**path** | Doit contenir le nom du dossier et le nom du fichier. Par exemple, si votre fonction contient un [déclencheur de file d’attente](functions-bindings-storage-queue.md), vous pouvez utiliser `"path": "samples-workitems/{queueTrigger}"` pour pointer vers un fichier dans le dossier `samples-workitems` avec un nom qui correspond au nom de fichier spécifié dans le message du déclencheur.   

## <a name="input---usage"></a>Entrée - utilisation

Dans les fonctions C#, vous liez les données de fichier d’entrée à l’aide d’un paramètre nommé dans la signature de la fonction, comme `<T> <name>`. `T` est le type de données dans lequel vous souhaitez désérialiser les données, et `name` le nom que vous avez spécifié dans la liaison d’entrée. Dans les fonctions Node.js, vous accédez aux données de fichier d’entrée en utilisant `context.bindings.<name>`.

Le fichier peut être désérialisé dans l’un des types suivants :

* N’importe quel [objet](https://msdn.microsoft.com/library/system.object.aspx) : utile pour les données de fichier sérialisées en JSON.
  Si vous déclarez un type d’entrée personnalisé (par exemple, `InputType`), Azure Functions tente de désérialiser les données JSON dans le type spécifié.
* Chaîne : utile pour les données de fichier texte.

Dans les fonctions C#, vous pouvez également effectuer une liaison vers un des types suivants (le runtime Functions tente de désérialiser les données du fichier à l’aide de ce type) :

* `string`
* `byte[]`
* `Stream`
* `StreamReader`
* `TextReader`

## <a name="output"></a>Sortie

La liaison de sortie de fichier externe Azure vous permet d’écrire des fichiers sur un dossier externe dans votre fonction.

## <a name="output---example"></a>Sortie - exemple

Consultez l’[exemple de liaison d’entrée](#input---example).

## <a name="output---configuration"></a>Sortie - configuration

Le tableau suivant décrit les propriétés de configuration de liaison que vous définissez dans le fichier *function.json*.

|Propriété function.json | Description|
|---------|---------|----------------------|
|**type** | Cette propriété doit être définie sur `apiHubFile`. Cette propriété est définie automatiquement lorsque vous créez le déclencheur dans le portail Azure.|
|**direction** | Cette propriété doit être définie sur `out`. Cette propriété est définie automatiquement lorsque vous créez le déclencheur dans le portail Azure. |
|**name** | Nom de la variable qui représente l’élément d’événement dans le code de la fonction. | 
|**Connexion**| Identifie le paramètre d’application qui stocke la chaîne de connexion. Le paramètre d’application est automatiquement créé quand vous ajoutez une connexion dans l’interface utilisateur d’intégration dans le portail Azure.|
|**path** | Doit contenir le nom du dossier et le nom du fichier. Par exemple, si votre fonction contient un [déclencheur de file d’attente](functions-bindings-storage-queue.md), vous pouvez utiliser `"path": "samples-workitems/{queueTrigger}"` pour pointer vers un fichier dans le dossier `samples-workitems` avec un nom qui correspond au nom de fichier spécifié dans le message du déclencheur.   

## <a name="output---usage"></a>Sortie - utilisation

Dans les fonctions C#, vous liez le fichier de sortie à l’aide du paramètre `out` nommé dans la signature de la fonction, par exemple, `out <T> <name>`, où `T` est le type de données dans lequel vous souhaitez sérialiser les données, et `name` le nom que vous avez spécifié dans la liaison de sortie. Dans les fonctions Node.js, vous accédez au fichier de sortie en utilisant `context.bindings.<name>`.

Vous pouvez écrire dans le fichier de sortie à l’aide d’un des types suivants :

* N’importe quel [objet](https://msdn.microsoft.com/library/system.object.aspx) : utile pour la sérialisation JSON.
  Si vous déclarez un type de sortie personnalisée (par exemple, `out OutputType paramName`), Azure Functions tente de sérialiser un objet en JSON. Si le paramètre de sortie est Null quand la fonction s’arrête, le runtime Functions crée un fichier comme objet Null.
* Chaîne : (`out string paramName`) utile pour les données de fichier texte. Le runtime Functions crée un fichier uniquement si le paramètre de chaîne n’est pas Null quand la fonction s’arrête.

Dans les fonctions C#, vous pouvez également définir une sortie vers les types suivants :

* `TextWriter`
* `Stream`
* `CloudFileStream`
* `ICloudFile`
* `CloudBlockFile`
* `CloudPageFile`

## <a name="next-steps"></a>étapes suivantes

> [!div class="nextstepaction"]
> [En savoir plus sur les déclencheurs et les liaisons Azure Functions](functions-triggers-bindings.md)
