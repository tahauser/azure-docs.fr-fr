---
title: "Sécuriser une API web Azure Active Directory 2.0 à l’aide de Node.js | Microsoft Docs"
description: "Découvrez comment créer une API web Node.js qui accepte des jetons d’un compte Microsoft personnel, ou de comptes professionnels ou scolaires."
services: active-directory
documentationcenter: nodejs
author: navyasric
manager: mtillman
editor: 
ms.assetid: 0b572fc1-2aaf-4cb6-82de-63010fb1941d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 05/13/2017
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: f07e421feedf3c82da7be16434891cdbe6069038
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="secure-a-web-api-by-using-nodejs"></a>Sécuriser une API web à l’aide de Node.js
> [!NOTE]
> Certains scénarios et fonctionnalités d’Azure Active Directory ne fonctionnent pas avec le point de terminaison 2.0. Pour déterminer si vous devez utiliser le point de terminaison 2.0 ou 1.0, consultez les [limitations de 2.0](active-directory-v2-limitations.md).
> 
> 

Quand vous utilisez le point de terminaison Azure Active Directory (Azure AD) 2.0, vous pouvez utiliser des jetons d’accès [OAuth 2.0](active-directory-v2-protocols.md) pour protéger votre API web. Avec les jetons d’accès OAuth 2.0, les utilisateurs disposant à la fois d’un compte Microsoft personnel et de comptes professionnels ou scolaires peuvent accéder de façon sécurisée à votre API web.

*Passport* est un intergiciel d’authentification pour Node.js. Flexible et modulaire, Passport peut être intégré de façon transparente dans n’importe quelle application web Restify ou basée sur Express. Dans Passport, une gamme complète de stratégies prend en charge l’authentification à l’aide d’un nom d’utilisateur et d’un mot de passe, de Facebook, de Twitter ou d’autres options. Nous avons développé une stratégie pour Azure AD. Dans cet article, nous vous montrons comment installer le module, puis comment ajouter le plug-in `passport-azure-ad` d’Azure AD.

## <a name="download"></a>Télécharger
Le code associé à ce didacticiel est stocké [sur GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs). Pour suivre le didacticiel, vous pouvez [télécharger la structure de l’application au format .zip](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs/archive/skeleton.zip) ou bien la cloner :

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs.git```

Vous pouvez également obtenir l’application terminée à la fin de ce didacticiel.

## <a name="1-register-an-app"></a>1 : Inscrire une application
Créez une application sur [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) ou suivez cette [procédure détaillée](active-directory-v2-app-registration.md) pour inscrire une application. Assurez-vous d’effectuer les tâches suivantes :

* Copiez l’**ID d’application** affecté à votre application. Vous en aurez besoin pour ce didacticiel.
* ajouter la plateforme **Mobile** pour votre application ;
* Copiez **l’URI de redirection** provenant du portail. Vous devez utiliser la valeur d’URI par défaut `urn:ietf:wg:oauth:2.0:oob`.

## <a name="2-install-nodejs"></a>2 : Installer Node.js
Pour utiliser l’exemple de ce didacticiel, vous devez [installer Node.js](http://nodejs.org).

## <a name="3-install-mongodb"></a>3 : Installer MongoDB
Pour pouvoir utiliser cet exemple, vous devez [installer MongoDB](http://www.mongodb.org). Dans cet exemple, nous utilisons MongoDB pour rendre votre API REST persistante sur les instances de serveur.

> [!NOTE]
> Dans cet article, nous partons du principe que vous utilisez les points de terminaison d’installation et de serveur par défaut pour MongoDB : mongodb://localhost.
> 
> 

## <a name="4-install-the-restify-modules-in-your-web-api"></a>4 : Installer les modules Restify dans votre API web
Nous utilisons Restify pour créer notre API REST. Restify est une infrastructure d’application Node.js minimale et flexible qui est dérivée d’Express. Restify a un ensemble robuste de fonctionnalités que vous pouvez utiliser pour créer des API REST sur Connect.

### <a name="install-restify"></a>Installer Restify
1.  À l’invite de commandes, accédez au répertoire **azuread** :

    `cd azuread`

    Si le répertoire **azuread** n’existe pas, créez-le :

    `mkdir azuread`

2.  Installez Restify :

    `npm install restify`

    La sortie de cette commande doit ressembler à ceci :

    ```
    restify@2.6.1 node_modules/restify
    ├── assert-plus@0.1.4
    ├── once@1.3.0
    ├── deep-equal@0.0.0
    ├── escape-regexp-component@1.0.2
    ├── qs@0.6.5
    ├── tunnel-agent@0.3.0
    ├── keep-alive-agent@0.0.1
    ├── lru-cache@2.3.1
    ├── node-uuid@1.4.0
    ├── negotiator@0.3.0
    ├── mime@1.2.11
    ├── semver@2.2.1
    ├── spdy@1.14.12
    ├── backoff@2.3.0
    ├── formidable@1.0.14
    ├── verror@1.3.6 (extsprintf@1.0.2)
    ├── csv@0.3.6
    ├── http-signature@0.10.0 (assert-plus@0.1.2, asn1@0.1.11, ctype@0.5.2)
    └── bunyan@0.22.0(mv@0.0.5)
    ```

#### <a name="did-you-get-an-error"></a>Une erreur est-elle survenue ?
Sur certains systèmes d’exploitation, quand vous utilisez la commande `npm`, ce message peut apparaître : `Error: EPERM, chmod '/usr/local/bin/..'`. Après cette erreur, il vous est demandé d’essayer d’exécuter le compte en tant qu’administrateur. Si ceci se produit, utilisez la commande `sudo` pour exécuter `npm` avec un niveau de privilèges plus élevé.

#### <a name="did-you-get-an-error-related-to-dtrace"></a>Avez-vous reçu une erreur liée à DTrace ?
Quand vous installez Restify, ce message peut apparaître :

```Shell
clang: error: no such file or directory: 'HD/azuread/node_modules/restify/node_modules/dtrace-provider/libusdt'
make: *** [Release/DTraceProviderBindings.node] Error 1
gyp ERR! build error
gyp ERR! stack Error: `make` failed with exit code: two
gyp ERR! stack     at ChildProcess.onExit (/usr/local/lib/node_modules/npm/node_modules/node-gyp/lib/build.js:267:23)
gyp ERR! stack     at ChildProcess.EventEmitter.emit (events.js:98:17)
gyp ERR! stack     at Process.ChildProcess._handle.onexit (child_process.js:789:12)
gyp ERR! System Darwin 13.1.0
gyp ERR! command "node" "/usr/local/lib/node_modules/npm/node_modules/node-gyp/bin/node-gyp.js" "rebuild"
gyp ERR! cwd /Volumes/Development HD/azuread/node_modules/restify/node_modules/dtrace-provider
gyp ERR! node -v v0.10.11
gyp ERR! node-gyp -v v0.10.0
gyp ERR! not ok
npm WARN optional dep failed, continuing dtrace-provider@0.2.8
```

Restify fournit un mécanisme puissant pour suivre les appels REST en utilisant DTrace. Cependant, DTrace n’est pas disponible sur de nombreux systèmes d’exploitation. Vous pouvez ignorer sans problème ce message d’erreur.


## <a name="5-install-passportjs-in-your-web-api"></a>5 : Installer Passport.js dans votre API web
1.  À l’invite de commandes, accédez au répertoire **azuread**.

2.  Installez Passport.js :

    `npm install passport`

    La sortie de la commande doit ressembler à ceci :

    ```
     passport@0.1.17 node_modules\passport
    ├── pause@0.0.1
    └── pkginfo@0.2.3
    ```

## <a name="6-add-passport-azure-ad-to-your-web-api"></a>6 : Ajouter passport-azure-ad à votre API web
Ensuite, ajoutez la stratégie OAuth en utilisant passport-azuread. `passport-azuread` est une suite de stratégies qui connectent Azure AD à Passport. Nous utilisons cette stratégie pour les jetons du porteur dans cet exemple d’API REST.

> [!NOTE]
> Bien qu’OAuth 2.0 fournisse un framework dans lequel tout type de jeton connu peut être émis, seuls certains types de jetons sont couramment utilisés. Les jetons du porteur sont couramment utilisés pour protéger les points de terminaison. Il s’agit du type de jeton le plus fréquemment émis dans OAuth 2.0. De nombreuses implémentations d’OAuth 2.0 partent du principe que les jetons du porteur sont le seul type de jeton émis.
> 
> 

1.  À l’invite de commandes, accédez au répertoire **azuread**.

    `cd azuread`

2.  Installez le module `passport-azure-ad` de Passport.js :

    `npm install passport-azure-ad`

    La sortie de la commande doit ressembler à ceci :

    ```
    passport-azure-ad@1.0.0 node_modules/passport-azure-ad
    ├── xtend@4.0.0
    ├── xmldom@0.1.19
    ├── passport-http-bearer@1.0.1 (passport-strategy@1.0.0)
    ├── underscore@1.8.3
    ├── async@1.3.0
    ├── jsonwebtoken@5.0.2
    ├── xml-crypto@0.5.27 (xpath.js@1.0.6)
    ├── ursa@0.8.5 (bindings@1.2.1, nan@1.8.4)
    ├── jws@3.0.0 (jwa@1.0.1, base64url@1.0.4)
    ├── request@2.58.0 (caseless@0.10.0, aws-sign2@0.5.0, forever-agent@0.6.1, stringstream@0.0.4, tunnel-agent@0.4.1, oauth-sign@0.8.0, isstream@0.1.2, extend@2.0.1, json-stringify-safe@5.0.1, node-uuid@1.4.3, qs@3.1.0, combined-stream@1.0.5, mime-types@2.0.14, form-data@1.0.0-rc1, http-signature@0.11.0, bl@0.9.4, tough-cookie@2.0.0, hawk@2.3.1, har-validator@1.8.0)
    └── xml2js@0.4.9 (sax@0.6.1, xmlbuilder@2.6.4)
    ```

## <a name="7-add-mongodb-modules-to-your-web-api"></a>7 : Ajouter les modules MongoDB à votre API web
Dans cet exemple, nous utilisons MongoDB comme notre magasin de données. 

1.  Installez Mongoose, un plug-in largement utilisé, pour gérer les modèles et les schémas : 

    `npm install mongoose`

2.  Installez le pilote de base de données pour MongoDB (également appelé MongoDB) :

    `npm install mongodb`

## <a name="8-install-additional-modules"></a>8 : Installation de modules supplémentaires
Installez les autres modules nécessaires.

1.  À l’invite de commandes, accédez au répertoire **azuread** :

    `cd azuread`

2.  Entrez les commandes suivantes. Les commandes installent les modules suivants dans votre répertoire node_modules :

    *   `npm install crypto`
    *   `npm install assert-plus`
    *   `npm install posix-getopt`
    *   `npm install util`
    *   `npm install path`
    *   `npm install connect`
    *   `npm install xml-crypto`
    *   `npm install xml2js`
    *   `npm install xmldom`
    *   `npm install async`
    *   `npm install request`
    *   `npm install underscore`
    *   `npm install grunt-contrib-jshint@0.1.1`
    *   `npm install grunt-contrib-nodeunit@0.1.2`
    *   `npm install grunt-contrib-watch@0.2.0`
    *   `npm install grunt@0.4.1`
    *   `npm install xtend@2.0.3`
    *   `npm install bunyan`
    *   `npm update`

## <a name="9-create-a-serverjs-file-for-your-dependencies"></a>9 : Créer un fichier Server.js pour vos dépendances
Un fichier Server.js contient la plupart des fonctionnalités de votre serveur d’API web. Ajoutez la plus grande partie de votre code à ce fichier. À des fins de production, vous pouvez refactoriser les fonctionnalités en fichiers plus petits, comme pour des routes et des contrôleurs distincts. Dans cet article, nous utilisons Server.js à cet effet.

1.  À l’invite de commandes, accédez au répertoire **azuread** :

    `cd azuread`

2.  À l’aide d’un éditeur de votre choix, créez un fichier Server.js. Ajoutez les informations suivantes au fichier :

    ```Javascript
    'use strict';
    /**
    * Module dependencies.
    */
    var util = require('util');
    var assert = require('assert-plus');
    var mongoose = require('mongoose/');
    var bunyan = require('bunyan');
    var restify = require('restify');
    var config = require('./config');
    var passport = require('passport');
    var OIDCBearerStrategy = require('passport-azure-ad').OIDCStrategy;
    ```

3.  Enregistrez le fichier . Nous y reviendrons sous peu.

## <a name="10-create-a-config-file-to-store-your-azure-ad-settings"></a>10 : création d’un fichier de configuration pour stocker vos paramètres Azure AD
Ce fichier de code passe les paramètres de configuration de votre portail Azure AD à Passport.js. Vous avez créé ces valeurs de configuration quand vous avez ajouté l’API web au portail, au début de cet article. Copiez le code. Après quoi, nous vous expliquerons ce qu’il convient de placer dans les valeurs de ces paramètres.

1.  À l’invite de commandes, accédez au répertoire **azuread** :

    `cd azuread`

2.  Dans un éditeur, créez un fichier Config.js. Ajoutez les informations suivantes :

    ```Javascript
    // Don't commit this file to your public repos. This config is for first-run.
    exports.creds = {
    mongoose_auth_local: 'mongodb://localhost/tasklist', // Your Mongo auth URI goes here.
    issuer: 'https://sts.windows.net/**<your application id>**/',
    audience: '<your redirect URI>',
    identityMetadata: 'https://login.microsoftonline.com/common/.well-known/openid-configuration' // For Microsoft, you should never need to change this.
    };

    ```



### <a name="required-values"></a>Valeurs requises

*   **IdentityMetadata** : c’est ici que `passport-azure-ad` cherche vos données de configuration pour le fournisseur d’identité et les clés pour valider les jetons JWT (JSON Web Token). Si vous utilisez Azure AD, vous ne voulez probablement pas changer ceci.

*   **audience**: URI de redirection à partir du portail.

> [!NOTE]
> Changez régulièrement vos clés. Vérifiez que votre extraction est toujours effectuée à partir de l’URL « openid_keys » et que l’application peut accéder à Internet.
> 
> 

## <a name="11-add-the-configuration-to-your-serverjs-file"></a>11 : Ajouter la configuration à votre fichier Server.js
Votre application doit lire les valeurs à partir du fichier de configuration que vous venez de créer. Ajoutez le fichier .config en tant que ressource requise dans votre application. Définissez les variables globales sur celles qui se trouvent dans Config.js.

1.  À l’invite de commandes, accédez au répertoire **azuread** :

    `cd azuread`

2.  Dans un éditeur, ouvrez Server.js. Ajoutez les informations suivantes :

    ```Javascript
    var config = require('./config');
    ```

3.  Ajoutez une nouvelle section à Server.js :

    ```Javascript
    // Pass these options in to the ODICBearerStrategy.
    var options = {
    // The URL of the metadata document for your app. Put the keys for token validation from the URL found in the jwks_uri tag in the metadata.
    identityMetadata: config.creds.identityMetadata,
    issuer: config.creds.issuer,
    audience: config.creds.audience
    };
    // Array to hold signed-in users and the current signed-in user (owner).
    var users = [];
    var owner = null;
    // Your logger
    var log = bunyan.createLogger({
    name: 'Microsoft Azure Active Directory Sample'
    });
    ```

## <a name="12-add-the-mongodb-model-and-schema-information-by-using-mongoose"></a>12 : Ajouter les informations de modèle et de schéma MongoDB à l’aide de Mongoose
Ensuite, connectez ces trois fichiers dans un service d’API REST.

Dans cet article, nous utilisons MongoDB pour stocker nos tâches. Nous traitons de cela dans l’*étape 4*.

Dans le fichier Config.js que vous avez créé à l’étape 11, votre base de données est appelée *tasklist*. C’est ce que vous mettez à la fin de l’URL de connexion mongoose_auth_local. Vous n’avez pas besoin de créer cette base de données au préalable dans MongoDB. La base de données est créée lors de la première exécution de votre application serveur (en supposant que la base de données n’existe pas déjà).

Vous avez indiqué au serveur la base de données MongoDB à utiliser. Ensuite, vous devez écrire du code supplémentaire pour créer le modèle et le schéma pour les tâches de votre serveur.

### <a name="the-model"></a>Le modèle
Le modèle de schéma est élémentaire. Vous pouvez le développer si nécessaire. 

Le modèle de schéma a ces valeurs :

*   **NAME**. Personne affectée à la tâche. Il s’agit d’une valeur de type **string**.
*   **TASK**. Nom de la tâche. Il s’agit d’une valeur de type **string**.
*   **DATE**. Date d’échéance de la tâche. Il s’agit d’une valeur de type **datetime**.
*   **COMPLETED**. Indique si la tâche est terminée. Il s’agit d’une valeur de type **boolean**.

### <a name="create-the-schema-in-the-code"></a>Créer le schéma dans le code
1.  À l’invite de commandes, accédez au répertoire **azuread** :

    `cd azuread`

2.  Dans votre éditeur, ouvrez Server.js. Sous l’entrée de configuration, ajoutez les informations suivantes :

    ```Javascript
    // MongoDB setup.
    // Set up some configuration.
    var serverPort = process.env.PORT || 8080;
    var serverURI = (process.env.PORT) ? config.creds.mongoose_auth_mongohq : config.creds.mongoose_auth_local;
    // Connect to MongoDB.
    global.db = mongoose.connect(serverURI);
    var Schema = mongoose.Schema;
    log.info('MongoDB Schema loaded');
    ```

Ce code se connecte au serveur MongoDB. Il retourne également un objet de schéma.

#### <a name="using-the-schema-create-your-model-in-the-code"></a>En utilisant le schéma, créez votre modèle dans le code.
Sous le code précédent, ajoutez le code suivant :

```Javascript
// Create a basic schema to store your tasks and users.
var TaskSchema = new Schema({
owner: String,
task: String,
completed: Boolean,
date: Date
});
// Use the schema to register a model.
mongoose.model('Task', TaskSchema);
var Task = mongoose.model('Task');
```

Comme vous pouvez le voir dans le code, vous créez d’abord votre schéma. Ensuite, vous créez un objet de modèle. Vous utilisez l’objet de modèle pour stocker vos données dans le code quand vous définissez vos **routes**.

## <a name="13-add-your-routes-for-your-task-rest-api-server"></a>13 : Ajouter vos routes pour votre serveur API REST de tâches
Maintenant que vous disposez d’un modèle de base de données, ajoutez les routes que vous utiliserez pour le serveur d’API REST.

### <a name="about-routes-in-restify"></a>À propos des routes dans Restify
Les routes dans Restify fonctionnent exactement de la même façon que quand vous utilisez la pile Express. Vous définissez des itinéraires à l’aide de l’URI qui, selon vous, sera appelé par les applications clientes. En général, vous définissez vos itinéraires dans un fichier distinct. Dans ce didacticiel, nous avons mis nos routes dans Server.js. Pour une utilisation en production, nous vous recommandons de factoriser ces routes dans leur propre fichier.

Voici un exemple de modèle d’une route Restify :

```Javascript
function createObject(req, res, next) {
// Do work on object.
_object.name = req.params.object; // Passed value is in req.params under object.
///...
return next(); // Keep the server going.
}
....
server.post('/service/:add/:object', createObject); // calls createObject on routes that match this.
```


Il s’agit d’un modèle très basique. Restify (et Express) fournit des fonctionnalités bien plus avancées, comme la possibilité de définir des types d’applications et le routage complexe entre différents points de terminaison.

#### <a name="add-default-routes-to-your-server"></a>Ajouter des itinéraires par défaut à votre serveur
Ajoutez les routes CRUD de base : **create**, **retrieve**, **update** et **delete**.

1.  À l’invite de commandes, accédez au répertoire **azuread** :

    `cd azuread`

2.  Dans un éditeur, ouvrez Server.js. Ajoutez les informations suivantes en dessous des entrées de base de données créées précédemment :

    ```Javascript
    /**
    *
    * APIs for your REST task server
    */
    // Create a task.
    function createTask(req, res, next) {
    // Resitify currently has a bug that doesn't allow you to set default headers.
    // These headers comply with CORS, and allow you to use MongoDB Server as your response to any origin.
    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "X-Requested-With");
    // Create a new task model, fill it, and save it to MongoDB.
    var _task = new Task();
    if (!req.params.task) {
    req.log.warn({
    params: p
    }, 'createTodo: missing task');
    next(new MissingTaskError());
    return;
    }
    _task.owner = owner;
    _task.task = req.params.task;
    _task.date = new Date();
    _task.save(function(err) {
    if (err) {
    req.log.warn(err, 'createTask: unable to save');
    next(err);
    } else {
    res.send(201, _task);
    }
    });
    return next();
    }
    // Delete a task by name.
    function removeTask(req, res, next) {
    Task.remove({
    task: req.params.task,
    owner: owner
    }, function(err) {
    if (err) {
    req.log.warn(err,
    'removeTask: unable to delete %s',
    req.params.task);
    next(err);
    } else {
    log.info('Deleted task:', req.params.task);
    res.send(204);
    next();
    }
    });
    }
    // Delete all tasks.
    function removeAll(req, res, next) {
    Task.remove();
    res.send(204);
    return next();
    }
    // Get a specific task based on name.
    function getTask(req, res, next) {
    log.info('getTask was called for: ', owner);
    Task.find({
    owner: owner
    }, function(err, data) {
    if (err) {
    req.log.warn(err, 'get: unable to read %s', owner);
    next(err);
    return;
    }
    res.json(data);
    });
    return next();
    }
    /// Returns the list of TODOs that were loaded.
    function listTasks(req, res, next) {
    // Resitify currently has a bug that doesn't allow you to set default headers.
    // These headers comply with CORS, and allow us to use MongoDB Server as our response to any origin.
    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "X-Requested-With");
    log.info("listTasks was called for: ", owner);
    Task.find({
    owner: owner
    }).limit(20).sort('date').exec(function(err, data) {
    if (err)
    return next(err);
    if (data.length > 0) {
    log.info(data);
    }
    if (!data.length) {
    log.warn(err, "There are no tasks in the database. Add one!");
    }
    if (!owner) {
    log.warn(err, "You did not pass an owner when listing tasks.");
    } else {
    res.json(data);
    }
    });
    return next();
    }
    ```

### <a name="add-error-handling-for-the-routes"></a>Ajouter la gestion des erreurs pour les itinéraires
Ajoutez une gestion des erreurs afin de communiquer avec le client sur le problème rencontré.

Ajoutez le code suivant sous le code que vous avez déjà écrit :

```Javascript
///--- Errors for communicating something more information back to the client.
function MissingTaskError() {
restify.RestError.call(this, {
statusCode: 409,
restCode: 'MissingTask',
message: '"task" is a required parameter',
constructorOpt: MissingTaskError
});
this.name = 'MissingTaskError';
}
util.inherits(MissingTaskError, restify.RestError);
function TaskExistsError(owner) {
assert.string(owner, 'owner');
restify.RestError.call(this, {
statusCode: 409,
restCode: 'TaskExists',
message: owner + ' already exists',
constructorOpt: TaskExistsError
});
this.name = 'TaskExistsError';
}
util.inherits(TaskExistsError, restify.RestError);
function TaskNotFoundError(owner) {
assert.string(owner, 'owner');
restify.RestError.call(this, {
statusCode: 404,
restCode: 'TaskNotFound',
message: owner + ' was not found',
constructorOpt: TaskNotFoundError
});
this.name = 'TaskNotFoundError';
}
util.inherits(TaskNotFoundError, restify.RestError);
```


## <a name="14-create-your-server"></a>14 : Créer votre serveur
La dernière chose à faire consiste à ajouter votre instance de serveur. L’instance de serveur gère vos appels.

Restify (et Express) permettent une personnalisation avancée, que vous pouvez utiliser avec un serveur d’API REST. Dans ce didacticiel, nous utilisons la configuration la plus simple.

```Javascript
/**
* Your server
*/
var server = restify.createServer({
name: "Microsoft Azure Active Directory TODO Server",
version: "2.0.1"
});
// Ensure that you don't drop data on uploads.
server.pre(restify.pre.pause());
// Clean up imprecise paths like //todo//////1//.
server.pre(restify.pre.sanitizePath());
// Handle annoying user agents (curl).
server.pre(restify.pre.userAgentConnection());
// Set a per-request Bunyan logger (with requestid filled in).
server.use(restify.requestLogger());
// Allow 5 requests/second by IP address, and burst to 10.
server.use(restify.throttle({
burst: 10,
rate: 5,
ip: true,
}));
// Use common commands, such as:
server.use(restify.acceptParser(server.acceptable));
server.use(restify.dateParser());
server.use(restify.queryParser());
server.use(restify.gzipResponse());
server.use(restify.bodyParser({
mapParams: true
}));
```
## <a name="15-add-the-routes-without-authentication-for-now"></a>15 : Ajouter les routes (pour l’instant sans authentification)
```Javascript
/// Use CRUD to add the real handlers.
/**
/*
/* Each of these handlers is protected by your Open ID Connect Bearer strategy. Invoke 'oidc-bearer'
/* in the pasport.authenticate() method. Because REST is stateless, set 'session: false'. You 
/* don't need to maintain session state. You can experiment with removing API protection.
/* To do this, remove the passport.authenticate() method:
/*
/* server.get('/tasks', listTasks);
/*
**/
server.get('/tasks', listTasks);
server.get('/tasks', listTasks);
server.get('/tasks/:owner', getTask);
server.head('/tasks/:owner', getTask);
server.post('/tasks/:owner/:task', createTask);
server.post('/tasks', createTask);
server.del('/tasks/:owner/:task', removeTask);
server.del('/tasks/:owner', removeTask);
server.del('/tasks', removeTask);
server.del('/tasks', removeAll, function respond(req, res, next) {
res.send(204);
next();
});
// Register a default '/' handler
server.get('/', function root(req, res, next) {
var routes = [
'GET /',
'POST /tasks/:owner/:task',
'POST /tasks (for JSON body)',
'GET /tasks',
'PUT /tasks/:owner',
'GET /tasks/:owner',
'DELETE /tasks/:owner/:task'
];
res.send(200, routes);
next();
});
server.listen(serverPort, function() {
var consoleMessage = '\n Microsoft Azure Active Directory Tutorial';
consoleMessage += '\n +++++++++++++++++++++++++++++++++++++++++++++++++++++';
consoleMessage += '\n %s server is listening at %s';
consoleMessage += '\n Open your browser to %s/tasks\n';
consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n';
consoleMessage += '\n !!! why not try a $curl -isS %s | json to get some ideas? \n';
consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n\n';
});
```
## <a name="16-run-the-server"></a>16 : Exécuter le serveur
Il est judicieux de tester votre serveur avant d’ajouter l’authentification.

Pour tester votre serveur, le plus simple consiste à utiliser curl à une invite de commandes. Pour ce faire, vous avez besoin d’un utilitaire simple que vous pouvez utiliser pour analyser la sortie au format JSON. 

1.  Installez l’outil JSON que nous utilisons dans les exemples suivants :

    `$npm install -g jsontool`

    Cette opération installe totalement l’outil JSON.

2.  Vérifiez que votre instance MongoDB est en cours d’exécution :

    `$sudo mongod`

3.  Accédez au répertoire **azuread**, puis exécutez curl :

    `$ cd azuread`
    `$ node server.js`

    `$ curl -isS http://127.0.0.1:8080 | json`

    ```Shell
    HTTP/1.1 2.0OK
    Connection: close
    Content-Type: application/json
    Content-Length: 171
    Date: Tue, 14 Jul 2015 05:43:38 GMT
    [
    "GET /",
    "POST /tasks/:owner/:task",
    "POST /tasks (for JSON body)",
    "GET /tasks",
    "PUT /tasks/:owner",
    "GET /tasks/:owner",
    "DELETE /tasks/:owner/:task"
    ]
    ```

4.  Pour ajouter une tâche :

    `$ curl -isS -X POST http://127.0.0.1:8888/tasks/brandon/Hello`

    La réponse doit être la suivante :

    ```Shell
    HTTP/1.1 201 Created
    Connection: close
    Access-Control-Allow-Origin: *
    Access-Control-Allow-Headers: X-Requested-With
    Content-Type: application/x-www-form-urlencoded
    Content-Length: 5
    Date: Tue, 04 Feb 2014 01:02:26 GMT
    Hello
    ```

5.  Répertoriez les tâches pour Brandon :

    `$ curl -isS http://127.0.0.1:8080/tasks/brandon/`

Si toutes ces commandes s’exécutent sans erreur, vous êtes prêt à ajouter OAuth au serveur d’API REST.

*Vous avez maintenant un serveur d’API REST avec MongoDB !*

## <a name="17-add-authentication-to-your-rest-api-server"></a>17 : Ajouter une authentification à votre serveur d’API REST
Maintenant que vous avez une API REST en cours d’exécution, configurez-la pour l’utiliser avec Azure AD.

À l’invite de commandes, accédez au répertoire **azuread** :

`cd azuread`

### <a name="use-the-oidcbearerstrategy-thats-included-with-passport-azure-ad"></a>Utiliser la stratégie oidcbearerstrategy incluse avec passport-azure-ad
Jusqu’à présent, vous avez créé un serveur REST TODO classique sans aucune gestion des autorisations. Ajoutez maintenant l’authentification.

Indiquez d’abord que vous voulez utiliser Passport. Placez ceci juste après votre configuration précédente du serveur :

```Javascript
// Start using Passport.js.

server.use(passport.initialize()); // Starts passport
server.use(passport.session()); // Provides session support
```

> [!TIP]
> Quand vous écrivez des API, il est judicieux de toujours lier les données à un élément unique du jeton, dont un utilisateur ne peut pas usurper l’identité. Quand ce serveur stocke des éléments TODO, il le fait en fonction de l’ID d’abonnement de l’utilisateur dans le jeton (appelé via token.sub). Vous placez le token.sub dans le champ « owner ». Ceci garantit que seul cet utilisateur peut accéder à ses éléments TODO. Personne d’autre ne peut accéder aux éléments TODO qui ont été entrés. Il n’existe pas d’exposition dans l’API pour « owner ». Un utilisateur externe peut demander les éléments TODO d’autres utilisateurs, même s’ils sont authentifiés.
> 
> 

Ensuite, utilisez la stratégie Open ID Connect Bearer qui est fournie avec `passport-azure-ad`. Placez ceci après ce que vous avez collé précédemment :

```Javascript
/**
/*
/* Calling the OIDCBearerStrategy and managing users.
/*
/* Because of the Passport pattern, you need to manage users and info tokens
/* with a FindorCreate() method. The method must be provided by the implementor.
/* In the following code, you autoregister any user and implement a FindById().
/* It's a good idea to do something more advanced.
**/
var findById = function(id, fn) {
for (var i = 0, len = users.length; i < len; i++) {
var user = users[i];
if (user.sub === id) {
log.info('Found user: ', user);
return fn(null, user);
}
}
return fn(null, null);
};
var oidcStrategy = new OIDCBearerStrategy(options,
function(token, done) {
log.info('verifying the user');
log.info(token, 'was the token retrieved');
findById(token.sub, function(err, user) {
if (err) {
return done(err);
}
if (!user) {
// "Auto-registration"
log.info('User was added automatically, because they were new. Their sub is: ', token.sub);
users.push(token);
owner = token.sub;
return done(null, token);
}
owner = token.sub;
return done(null, user, token);
});
}
);
passport.use(oidcStrategy);
```

Passport utilise un modèle similaire pour toutes ses stratégies (Twitter, Facebook, etc.). Tous les enregistreurs de stratégie adhèrent au modèle. Transmettez à la stratégie une `function()` qui utilise un jeton et `done` comme paramètres. La stratégie est retournée une fois qu’elle a effectué tout son travail. Stockez l’utilisateur et mettez le jeton de côté pour ne pas avoir à le redemander.

> [!IMPORTANT]
> Le code précédent accepte n’importe quel utilisateur qui peut s’authentifier auprès de votre serveur. C’est ce qu’on appelle l’enregistrement automatique. Sur un serveur de production, vous n’allez laisser entrer personne sans d’abord lui imposer un processus d’inscription de votre choix. C’est généralement le modèle que vous voyez dans les applications consommateur. L’application peut permettre de vous inscrire sur Facebook, mais elle vous demande ensuite d’entrer des informations supplémentaires. Si vous n’utilisiez pas un programme de ligne de commande pour ce didacticiel, vous pouvez extraire l’e-mail de l’objet jeton qui est retourné. Ensuite, vous pouvez demander à l’utilisateur d’entrer des informations supplémentaires. Comme il s’agit d’un serveur de test, vous ajoutez l’utilisateur directement à la base de données en mémoire.
> 
> 

### <a name="protect-endpoints"></a>Protéger les points de terminaison
Protégez les points de terminaison en spécifiant l’appel **passport.authenticate()** avec le protocole que vous voulez utiliser.

Vous pouvez modifier votre route dans le code de votre serveur pour une option plus avancée :

```Javascript
server.get('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), listTasks);
server.get('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), listTasks);
server.get('/tasks/:owner', passport.authenticate('oidc-bearer', {
session: false
}), getTask);
server.head('/tasks/:owner', passport.authenticate('oidc-bearer', {
session: false
}), getTask);
server.post('/tasks/:owner/:task', passport.authenticate('oidc-bearer', {
session: false
}), createTask);
server.post('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), createTask);
server.del('/tasks/:owner/:task', passport.authenticate('oidc-bearer', {
session: false
}), removeTask);
server.del('/tasks/:owner', passport.authenticate('oidc-bearer', {
session: false
}), removeTask);
server.del('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), removeTask);
server.del('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), removeAll, function respond(req, res, next) {
res.send(204);
next();
});
```

## <a name="18-run-your-server-application-again"></a>18 : Réexécuter votre application serveur
Là encore, utilisez curl pour voir si vous avez une protection OAuth 2.0 pour vos points de terminaison. Faites-le avant d’exécuter un SDK client sur ce point de terminaison. Les en-têtes retournés doivent vous indiquer si l’authentification fonctionne correctement.

1.  Vérifiez que votre instance MongoDB est en cours d’exécution :

    `$sudo mongod`

2.  Accédez au répertoire **azuread** puis utilisez curl :

    `$ cd azuread`

    `$ node server.js`

3.  Essayez une commande basique, comme POST :

    `$ curl -isS -X POST http://127.0.0.1:8080/tasks/brandon/Hello`

    ```Shell
    HTTP/1.1 401 Unauthorized
    Connection: close
    WWW-Authenticate: Bearer realm="Users"
    Date: Tue, 14 Jul 2015 05:45:03 GMT
    Transfer-Encoding: chunked
    ```

Une réponse 401 indique que la couche Passport tente de rediriger vers le point de terminaison autorisé, ce qui est exactement ce que vous voulez.

*Vous disposez maintenant d’un service API REST qui utilise OAuth 2.0 !*

Vous êtes allé aussi loin que possible avec ce serveur sans utiliser un client compatible OAuth 2.0. Pour cela, vous devez consulter un autre didacticiel.

## <a name="next-steps"></a>Étapes suivantes
Pour référence, l’exemple terminé (sans vos valeurs de configuration) est fourni en tant que [fichier .zip](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs/archive/complete.zip). Vous pouvez également le cloner à partir de GitHub :

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs.git```

Vous pouvez maintenant aborder des rubriques plus avancées. Vous pouvez essayer [Sécuriser une application Node.js en utilisant le point de terminaison v2.0](active-directory-v2-devquickstarts-node-web.md).

Voici quelques ressources supplémentaires :

* [Guide du développeur Azure AD v2.0](active-directory-appmodel-v2-overview.md)
* [Balise « azure-active-directory » dans Stack Overflow](http://stackoverflow.com/questions/tagged/azure-active-directory)

### <a name="get-security-updates-for-our-products"></a>Obtenir les mises à jour de sécurité de nos produits
Nous vous encourageons à vous inscrire pour être averti quand des incidents de sécurité se produisent. Sur la page [Microsoft Technical Security Notifications](https://technet.microsoft.com/security/dd252948), abonnez-vous aux alertes d’avis de sécurité.

