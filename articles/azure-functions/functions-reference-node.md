---
title: Information de référence pour les développeurs JavaScript sur Azure Functions | Microsoft Docs
description: Découvrez comment développer des fonctions à l’aide de JavaScript.
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: ''
tags: ''
keywords: azure functions, fonctions, traitement des événements, webhooks, calcul dynamique, architecture sans serveur
ms.assetid: 45dedd78-3ff9-411f-bb4b-16d29a11384c
ms.service: functions
ms.devlang: nodejs
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/04/2018
ms.author: tdykstra
ms.openlocfilehash: 9d63c95c849c8ef6011557c72240e56071ba614f
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="azure-functions-javascript-developer-guide"></a>Guide des développeurs JavaScript sur Azure Functions
[!INCLUDE [functions-selector-languages](../../includes/functions-selector-languages.md)]

L’expérience JavaScript pour Azure Functions facilite l’exportation d’une fonction qui reçoit un objet `context` pour communiquer avec le runtime et pour recevoir et envoyer des données par le biais des liaisons.

Cet article suppose que vous ayez déjà lu l’article [Informations de référence pour les développeurs sur Azure Functions](functions-reference.md).

## <a name="exporting-a-function"></a>Exporter une fonction
Toutes les fonctions JavaScript doivent exporter un seul `function` par le biais de `module.exports` pour que le runtime trouve la fonction et l’exécute. Cette fonction doit toujours inclure un objet `context` .

```javascript
// You must include a context, but other arguments are optional
module.exports = function(context) {
    // Additional inputs can be accessed by the arguments property
    if(arguments.length === 4) {
        context.log('This function has 4 inputs');
    }
};
// or you can include additional inputs in your arguments
module.exports = function(context, myTrigger, myInput, myOtherInput) {
    // function logic goes here :)
};
```

Les liaisons de `direction === "in"` sont transmises en tant qu’arguments de fonction, ce qui signifie que vous pouvez utiliser [`arguments`](https://msdn.microsoft.com/library/87dw3w1k.aspx) pour gérer de manière dynamique les nouvelles entrées (par exemple, en utilisant `arguments.length` pour effectuer une itération sur toutes vos entrées). Cette fonctionnalité est pratique si vous ne disposez que d’un déclencheur sans entrée supplémentaire, dans la mesure où vous pouvez accéder de manière prévisible aux données de votre déclencheur sans faire référence à votre objet `context`.

Les arguments sont toujours transmis à la fonction dans leur ordre d’apparition dans *function.json*, même si vous ne les spécifiez pas dans votre instruction d’exportation. Par exemple, si vous avez `function(context, a, b)` et le remplacez par `function(context, a)`, vous pouvez toujours obtenir la valeur `b` dans le code de fonction en faisant référence à `arguments[2]`.

Toutes les liaisons, quelle que soit leur direction, sont également transmises sur l’objet `context` (voir le script ci-dessous). 

## <a name="context-object"></a>Objet de contexte
Le runtime utilise un objet `context` pour transmettre des données vers et à partir de votre fonction et vous permettre de communiquer avec le runtime.

L’objet `context` est toujours le premier paramètre d’une fonction et doit toujours être inclus, car il possède des méthodes telles que `context.done` et `context.log` nécessaires pour utiliser correctement le runtime. Vous pouvez nommer l’objet comme vous le souhaitez (par exemple, `ctx` ou `c`).

```javascript
// You must include a context, but other arguments are optional
module.exports = function(context) {
    // function logic goes here :)
};
```

### <a name="contextbindings-property"></a>Propriété context.bindings

```
context.bindings
```
Retourne un objet nommé qui contient toutes vos données d’entrée et de sortie. Par exemple, la définition de liaison suivante dans votre *function.json* vous permet d’accéder au contenu de la file d’attente à partir de l’objet `context.bindings.myInput`. 

```json
{
    "type":"queue",
    "direction":"in",
    "name":"myInput"
    ...
}
```

```javascript
// myInput contains the input data, which may have properties such as "name"
var author = context.bindings.myInput.name;
// Similarly, you can set your output data
context.bindings.myOutput = { 
        some_text: 'hello world', 
        a_number: 1 };
```

### <a name="contextdone-method"></a>Méthode context.done
```
context.done([err],[propertyBag])
```

Informe le runtime que votre code est terminé. Vous devez appeler `context.done`. Sinon, le runtime ne sait pas que votre fonction est terminée et que l’exécution va expirer. 

La méthode `context.done` permet de transmettre une erreur définie par l’utilisateur au runtime ainsi qu’un conteneur de propriétés, qui remplaceront les propriétés de l’objet `context.bindings`.

```javascript
// Even though we set myOutput to have:
//  -> text: hello world, number: 123
context.bindings.myOutput = { text: 'hello world', number: 123 };
// If we pass an object to the done function...
context.done(null, { myOutput: { text: 'hello there, world', noNumber: true }});
// the done method will overwrite the myOutput binding to be: 
//  -> text: hello there, world, noNumber: true
```

### <a name="contextlog-method"></a>Méthode context.log  

```
context.log(message)
```
Vous permet d’écrire dans les journaux de console de streaming au niveau de trace par défaut. Des méthodes de journalisation supplémentaires sont disponibles sur `context.log` pour vous permettre d’écrire dans le journal de console à d’autres niveaux de trace :


| Méthode                 | Description                                |
| ---------------------- | ------------------------------------------ |
| **error(_message_)**   | Écrit dans la journalisation du niveau d’erreur, ou à un niveau inférieur.   |
| **warn(_message_)**    | Écrit dans la journalisation du niveau d’avertissement, ou à un niveau inférieur. |
| **info(_message_)**    | Écrit dans la journalisation du niveau d’information, ou à un niveau inférieur.    |
| **verbose(_message_)** | Écrit dans la journalisation du niveau détaillé.           |

L’exemple suivant écrit dans la console au niveau de trace d’avertissement :

```javascript
context.log.warn("Something has happened."); 
```
Vous pouvez définir le seuil du niveau de trace pour la journalisation dans le fichier host.json, ou le désactiver.  Pour plus d’informations sur l’écriture dans les journaux, consultez la section suivante.

## <a name="binding-data-type"></a>Type de données d’une liaison

Pour définir le type de données pour une liaison d’entrée, utilisez la propriété `dataType` dans la définition de la liaison. Par exemple, pour lire le contenu d’une requête HTTP au format binaire, utilisez le type `binary` :

```json
{
    "type": "httpTrigger",
    "name": "req",
    "direction": "in",
    "dataType": "binary"
}
```

Les autres options pour `dataType` sont `stream` et `string`.

## <a name="writing-trace-output-to-the-console"></a>Écrire la sortie de trace dans la console 

Dans Functions, vous utilisez les méthodes `context.log` pour écrire la sortie de trace dans la console. À ce stade, vous ne pouvez pas utiliser `console.log` pour écrire dans la console.

Lorsque vous appelez `context.log()`, votre message est écrit dans la console au niveau de trace par défaut, qui est le niveau de trace d’_informations_. Le code suivant écrit dans la console au niveau de trace d’informations :

```javascript
context.log({hello: 'world'});  
```

Il équivaut à ce qui suit :

```javascript
context.log.info({hello: 'world'});  
```

Le code suivant écrit dans la console au niveau de trace d’erreur :

```javascript
context.log.error("An error has occurred.");  
```

Étant donné que le niveau d’_erreur_ constitue le niveau de trace le plus élevé, cette trace est écrite dans la sortie à tous les niveaux de trace tant que la journalisation est activée.  


Toutes les méthodes `context.log` prennent en charge le même format de paramètre que celui pris en charge par la [méthode util.format](https://nodejs.org/api/util.html#util_util_format_format) Node.js. Prenons le code suivant qui écrit dans la console en utilisant le niveau de trace par défaut :

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=' + req.originalUrl);
context.log('Request Headers = ' + JSON.stringify(req.headers));
```

Vous pouvez écrire ce même code au format suivant :

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);
context.log('Request Headers = ', JSON.stringify(req.headers));
```

### <a name="configure-the-trace-level-for-console-logging"></a>Configurer le niveau de trace pour la journalisation de la console

Functions vous permet de définir le niveau de trace du seuil pour écrire dans la console, ce qui facilite le contrôle de l’écriture des traces dans la console à partir de vos fonctions. Utilisez la propriété `tracing.consoleLevel` dans le fichier host.json pour définir le seuil de toutes les traces écrites dans la console. Ce paramètre s’applique à toutes les fonctions dans votre Function App. L’exemple suivant définit le seuil de trace permettant d’activer la journalisation détaillée :

```json
{ 
    "tracing": {      
        "consoleLevel": "verbose"     
    }
}  
```

Les valeurs de **consoleLevel** correspondent aux noms des méthodes `context.log`. Pour désactiver toutes les journalisations de trace dans la console, définissez **consoleLevel** sur _désactivé_. Pour plus d’informations, consultez l’article de référence sur [host.json](functions-host-json.md).

## <a name="http-triggers-and-bindings"></a>Déclencheurs et liaisons HTTP

Les déclencheurs HTTP et webhook ainsi que les liaisons de sortie HTTP utilisent les objets de requête et de réponse pour représenter la messagerie HTTP.  

### <a name="request-object"></a>Objet Requête

L’objet `request` dispose des propriétés suivantes :

| Propriété      | Description                                                    |
| ------------- | -------------------------------------------------------------- |
| _body_        | Objet qui contient le corps de la demande.               |
| _headers_     | Objet qui contient les en-têtes de la demande.                   |
| _method_      | Méthode HTTP de la demande.                                |
| _originalUrl_ | URL de la demande.                                        |
| _params_      | Objet qui contient les paramètres de routage de la demande. |
| _query_       | Objet qui contient les paramètres de la requête.                  |
| _rawBody_     | Corps du message en tant que chaîne.                           |


### <a name="response-object"></a>Objet Réponse

L’objet `response` dispose des propriétés suivantes :

| Propriété  | Description                                               |
| --------- | --------------------------------------------------------- |
| _body_    | Objet qui contient le corps de la réponse.         |
| _headers_ | Objet qui contient les en-têtes de la réponse.             |
| _isRaw_   | Indique que la mise en forme est ignorée pour la réponse.    |
| _statut_  | Code d’état HTTP de la réponse.                     |

### <a name="accessing-the-request-and-response"></a>Accès à la demande et à la réponse 

Lorsque vous travaillez avec des déclencheurs HTTP, trois méthodes vous permettent d’accéder à des objets de demande et de réponse HTTP :

+ À partir de des liaisons dites d’entrée et de sortie. Avec cette méthode, le déclencheur HTTP et les liaisons fonctionnent de la même manière que toute autre liaison. L’exemple suivant définit l’objet de réponse en utilisant une liaison nommée `response` : 

    ```javascript
    context.bindings.response = { status: 201, body: "Insert succeeded." };
    ```

+ À partir des propriétés `req` et `res` sur l’objet `context`. Avec cette méthode, vous pouvez utiliser le modèle classique pour accéder aux données HTTP à partir de l’objet de contexte, au lieu d’utiliser le modèle `context.bindings.name` complet. L’exemple suivant montre comment accéder aux objets `req` et `res` sur `context` :

    ```javascript
    // You can access your http request off the context ...
    if(context.req.body.emoji === ':pizza:') context.log('Yay!');
    // and also set your http response
    context.res = { status: 202, body: 'You successfully ordered more coffee!' }; 
    ```

+ En appelant `context.done()`. Un type spécial de liaison HTTP renvoie la réponse transmise à la méthode `context.done()`. La liaison de sortie HTTP suivante définit un paramètre de sortie `$return` :

    ```json
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
    ``` 
    Cette liaison de sortie suppose que vous fournissiez la réponse lorsque vous appelez `done()` comme suit :

    ```javascript
     // Define a valid response object.
    res = { status: 201, body: "Insert succeeded." };
    context.done(null, res);   
    ```  

## <a name="node-version-and-package-management"></a>Version du nœud et gestion des packages

Le tableau suivant montre la version de Node.js qui est utilisée par chaque version majeure du runtime Functions :

| Version de Functions | Version de Node.js | 
|---|---|
| 1.x | 6.11.2 (verrouillée par le runtime) |
| 2.x  |> = 8.4.0 (version LTS 8.9.4 recommandée) Définissez la version à l’aide du [paramètre d’application](functions-how-to-use-azure-function-app-settings.md#settings) WEBSITE_NODE_DEFAULT_VERSION.|

Vous pouvez afficher la version que le runtime utilise en imprimant `process.version` à partir de n’importe quelle fonction.

Les étapes suivantes vous permettent d’inclure des packages dans votre Function App : 

1. Accédez à `https://<function_app_name>.scm.azurewebsites.net`

2. Cliquez sur **Console de débogage** > **CMD**.

3. Accédez à `D:\home\site\wwwroot`, puis faites glisser le fichier package.json vers le dossier **wwwroot** dans la partie supérieure de la page.  
    Il existe d’autres manières de télécharger des fichiers dans votre Function App. Pour plus d’informations, consultez [Comment mettre à jour les fichiers du conteneur de fonctions](functions-reference.md#fileupdate). 

4. Une fois le fichier package.json chargé, exécutez la commande `npm install` dans la **console d’exécution à distance Kudu**.  
    Les packages d’actions indiqués dans le fichier package.json sont téléchargés et Function App redémarre.

Une fois les packages nécessaires installés, vous pouvez les importer dans votre fonction en appelant `require('packagename')`, comme dans l’exemple suivant :

```javascript
// Import the underscore.js library
var _ = require('underscore');
var version = process.version; // version === 'v6.5.0'

module.exports = function(context) {
    // Using our imported underscore.js library
    var matched_names = _
        .where(context.bindings.myInput.names, {first: 'Carla'});
```

Vous devez définir un fichier `package.json` à la racine de votre Function App. Cela permet à toutes les fonctions de l’application de partager les mêmes packages mis en cache, pour des performances optimales. En cas de conflit de version, vous pouvez ajouter un fichier `package.json` dans le dossier d’une fonction spécifique pour le résoudre.  

## <a name="environment-variables"></a>Variables d’environnement
Pour obtenir une variable d’environnement ou une valeur de paramètre d’application, utilisez `process.env`, comme illustré dans l’exemple de code suivant :

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    context.log('Node.js timer trigger function ran!', timeStamp);   
    context.log(GetEnvironmentVariable("AzureWebJobsStorage"));
    context.log(GetEnvironmentVariable("WEBSITE_SITE_NAME"));

    context.done();
};

function GetEnvironmentVariable(name)
{
    return name + ": " + process.env[name];
}
```
## <a name="considerations-for-javascript-functions"></a>Considérations relatives aux fonctions JavaScript

Lorsque vous utilisez des fonctions JavaScript, tenez compte des considérations décrites dans les deux sections suivantes.

### <a name="choose-single-vcpu-app-service-plans"></a>Choisir des plans App Service à processeur virtuel unique

Lorsque vous créez une application de fonction qui utilise le plan App Service, nous vous recommandons de sélectionner un plan à processeur virtuel unique plutôt qu’un plan à plusieurs processeurs virtuels. À l’heure actuelle, Functions exécute les fonctions JavaScript plus efficacement sur des machines virtuelles à processeur virtuel unique. Le recours à de plus grandes machines virtuelles ne produit pas les améliorations de performances attendues. Le cas échéant, vous pouvez faire une mise à l’échelle horizontale manuellement en ajoutant des instances de machine virtuelle à processeur virtuel unique, ou vous pouvez activer la mise à l’échelle automatique. Pour plus d’informations, consultez [Mettre à l’échelle le nombre d’instances manuellement ou automatiquement](../monitoring-and-diagnostics/insights-how-to-scale.md?toc=%2fazure%2fapp-service-web%2ftoc.json).    

### <a name="typescript-and-coffeescript-support"></a>Prise en charge de typeScript et CoffeeScript
Comme il n’existe encore aucune prise en charge directe pour l’auto-compilation de TypeScript/CoffeeScript via le runtime, cela nécessite une gestion externe au runtime, au moment du déploiement. 

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations, consultez les ressources suivantes :

* [Meilleures pratiques pour Azure Functions](functions-best-practices.md)
* [Informations de référence pour les développeurs sur Azure Functions](functions-reference.md)
* [Azure Functions triggers and bindings (Déclencheurs et liaisons Azure Functions)](functions-triggers-bindings.md)

