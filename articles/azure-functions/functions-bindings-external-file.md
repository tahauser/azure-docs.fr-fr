---
title: "Liaisons de fichier externe Azure Functions (version préliminaire) | Microsoft Docs"
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
ms.openlocfilehash: c7a1ff4d4488b37b1969edfbb6f935eccd63413c
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/30/2017
---
# <a name="azure-functions-external-file-bindings-preview"></a>Liaisons de fichiers externes Azure Functions (version préliminaire)
Cet article montre comment manipuler les fichiers à partir de différents fournisseurs SaaS (par exemple, OneDrive, Dropbox) au sein de votre fonction en utilisant des liaisons intégrées. Azure Functions prend en charge les liaisons de déclencheur, d’entrée et de sortie pour les fichiers externes.

Cette liaison crée des connexions d’API aux fournisseurs SaaS, ou utilise des connexions d’API existantes à partir du groupe de ressources de votre application Function App.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="supported-file-connections"></a>Connexions de fichiers prises en charge

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
> Les connexions aux fichiers externes peuvent également servir dans les applications [Azure Logic Apps](https://docs.microsoft.com/azure/connectors/apis-list)

## <a name="external-file-trigger-binding"></a>Liaison de déclencheur de fichier externe

Le déclencheur de fichier externe Azure vous permet de surveiller un dossier à distance et d’exécuter votre code de fonction en cas de détection de modifications.

Le déclencheur de fichier externe utilise les objets JSON suivants dans le tableau `bindings` de function.json

```json
{
  "type": "apiHubFileTrigger",
  "name": "<Name of input parameter in function signature>",
  "direction": "in",
  "path": "<folder to monitor, and optionally a name pattern - see below>",
  "connection": "<name of external file connection - see above>"
}
```
<!---
See one of the following subheadings for more information:

* [Name patterns](#pattern)
* [File receipts](#receipts)
* [Handling poison files](#poison)
--->

<a name="pattern"></a>

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

<a name="receipts"></a>

<!--- ### File receipts
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
<a name="poison"></a>

### <a name="handling-poison-files"></a>Gestion des fichiers incohérents
En cas d’échec d’une fonction de déclenchement de fichier externe, Azure Functions réessaie cette fonction jusqu’à 5 fois par défaut (première tentative comprise) pour un fichier donné.
Si toutes les 5 tentatives échouent, Functions ajoute un message à une file d’attente Stockage nommée *webjobs-apihubtrigger-poison*. Le message en file d’attente associé aux fichiers incohérents correspond à un objet JSON, qui contient les propriétés suivantes :

* FunctionId (au format *&lt;nom de l’application de fonctions>*.Functions.*&lt;nom de la fonction>*)
* FileType
* FolderName
* FileName
* ETag (identificateur de version du fichier, par exemple : « 0x8D1DC6E70A277EF »)


<a name="triggerusage"></a>

## <a name="trigger-usage"></a>Utilisation du déclencheur
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

## <a name="trigger-sample"></a>Exemple de déclencheur
Supposons le code function.json suivant, qui définit un déclencheur de fichier externe :

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

Consultez l’exemple dans le langage de votre choix pour voir comment enregistrer le contenu de chaque fichier ajouté au dossier surveillé.

* [C#](#triggercsharp)
* [Node.JS](#triggernodejs)

<a name="triggercsharp"></a>

### <a name="trigger-usage-in-c"></a>Utilisation du déclencheur en C# #

```cs
public static void Run(string myFile, TraceWriter log)
{
    log.Info($"C# File trigger function processed: {myFile}");
}
```

<!--
<a name="triggerfsharp"></a>
### Trigger usage in F# ##
```fsharp

```
-->

<a name="triggernodejs"></a>

### <a name="trigger-usage-in-nodejs"></a>Utilisation du déclencheur en Node.js

```javascript
module.exports = function(context) {
    context.log('Node.js File trigger function processed', context.bindings.myFile);
    context.done();
};
```

<a name="input"></a>

## <a name="external-file-input-binding"></a>Liaison d’entrée de fichier externe
La liaison d’entrée de fichier externe Azure vous permet d’utiliser un fichier à partir d’un dossier externe dans votre fonction.

L’entrée de fichier externe d’une fonction utilise les objets JSON suivants dans le tableau `bindings` de function.json :

```json
{
  "name": "<Name of input parameter in function signature>",
  "type": "apiHubFile",
  "direction": "in",
  "path": "<Path of input file - see below>",
  "connection": "<name of external file connection>"
},
```

Notez les points suivants :

* `path` doit contenir le nom du dossier et le nom du fichier. Par exemple, si votre fonction contient un [déclencheur de file d’attente](functions-bindings-storage-queue.md), vous pouvez utiliser `"path": "samples-workitems/{queueTrigger}"` pour pointer vers un fichier dans le dossier `samples-workitems` avec un nom qui correspond au nom de fichier spécifié dans le message du déclencheur.   

<a name="inputusage"></a>

## <a name="input-usage"></a>Utilisation en entrée
Dans les fonctions C#, vous liez les données de fichier d’entrée à l’aide d’un paramètre nommé dans la signature de la fonction, comme `<T> <name>`.
Où `T` est le type de données dans lequel vous souhaitez désérialiser les données, et `paramName` le nom que vous avez spécifié dans la [liaison d’entrée](#input). Dans les fonctions Node.js, vous accédez aux données de fichier d’entrée en utilisant `context.bindings.<name>`.

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


<a name="output"></a>

## <a name="external-file-output-binding"></a>Liaison de sortie de fichier externe
La liaison de sortie de fichier externe Azure vous permet d’écrire des fichiers sur un dossier externe dans votre fonction.

La sortie de fichier externe pour une fonction utilise les objets JSON suivants dans le tableau `bindings` de function.json :

```json
{
  "name": "<Name of output parameter in function signature>",
  "type": "apiHubFile",
  "direction": "out",
  "path": "<Path of input file - see below>",
  "connection": "<name of external file connection>"
}
```

Notez les points suivants :

* `path` doit contenir le nom du dossier et le nom du fichier sur lequel écrire. Par exemple, si votre fonction contient un [déclencheur de file d’attente](functions-bindings-storage-queue.md), vous pouvez utiliser `"path": "samples-workitems/{queueTrigger}"` pour pointer vers un fichier dans le dossier `samples-workitems` avec un nom qui correspond au nom de fichier spécifié dans le message du déclencheur.   

<a name="outputusage"></a>

## <a name="output-usage"></a>Utilisation en sortie
Dans les fonctions C#, vous liez le fichier de sortie à l’aide du paramètre `out` nommé dans la signature de la fonction, par exemple, `out <T> <name>`, où `T` est le type de données dans lequel vous souhaitez sérialiser les données, et `paramName` le nom que vous avez spécifié dans la [liaison de sortie](#output). Dans les fonctions Node.js, vous accédez au fichier de sortie en utilisant `context.bindings.<name>`.

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

<a name="outputsample"></a>

<a name="sample"></a>

## <a name="input--output-sample"></a>Entrée + Exemple de sortie
Supposons le code function.json suivant, qui définit un [déclencheur de file d’attente Stockage](functions-bindings-storage-queue.md), une entrée de fichier externe et une sortie de fichier externe :

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

Consultez l’exemple dans le langage de votre choix pour voir comment copier le fichier d’entrée dans le fichier de sortie.

* [C#](#incsharp)
* [Node.JS](#innodejs)

<a name="incsharp"></a>

### <a name="usage-in-c"></a>Utilisation en C# #

```cs
public static void Run(string myQueueItem, string myInputFile, out string myOutputFile, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    myOutputFile = myInputFile;
}
```

<!--
<a name="infsharp"></a>
### Input usage in F# ##
```fsharp

```
-->

<a name="innodejs"></a>

### <a name="usage-in-nodejs"></a>Utilisation en Node.js

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputFile = context.bindings.myInputFile;
    context.done();
};
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [En savoir plus sur les déclencheurs et les liaisons Azure Functions](functions-triggers-bindings.md)
