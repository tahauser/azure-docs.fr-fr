---
title: 'Azure AD B2C : sécuriser une API web à l’aide de Node.js | Microsoft Docs'
description: Comment créer une API web Node.js qui accepte les jetons d’un client B2C
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.devlang: javascript
ms.topic: article
ms.date: 01/07/2017
ms.author: davidmu
ms.openlocfilehash: 3ce0e43beec41599c842cd273cb7230660bdf8d8
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-ad-b2c-secure-a-web-api-by-using-nodejs"></a>Azure AD B2C : sécuriser une API web à l’aide de Node.js
<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Avec Azure Active Directory (Azure AD) B2C, vous pouvez sécuriser une API web à l’aide de jetons d’accès OAuth 2.0, ce qui permet aux applications clientes qui utilisent Azure AD B2C de s’authentifier auprès de l’API. Cet article vous indique comment créer une API « liste de tâches » permettant aux utilisateurs d’ajouter des tâches et de les répertorier. L’API web est sécurisée à l’aide d’Azure AD B2C et autorise uniquement les utilisateurs authentifiés à gérer leur liste de tâches.

> [!NOTE]
> Cet exemple a été écrit pour être connecté à notre [exemple d’application iOS B2C](active-directory-b2c-devquickstarts-ios.md). Effectuez d’abord la procédure pas à pas actuelle avant de passer à cet exemple.
>
>

**Passport** est un intergiciel d’authentification pour Node.js. Flexible et modulaire, Passport peut être installé discrètement dans toute application web basée sur Express ou Restify. Une gamme complète de stratégies prend en charge l’authentification à l’aide d’un nom d’utilisateur et d’un mot de passe, de Facebook, de Twitter,etc. Nous avons développé une stratégie pour Azure Active Directory (Azure AD). Installez ce module, puis ajoutez le plug-in `passport-azure-ad` Azure AD.

Pour effectuer cet exemple, vous devez procéder comme suit :

1. inscrivez une application auprès d’Azure AD ;
2. configurez votre application pour utiliser le plug-in `passport-azure-ad` de Passport ;
3. configurez une application cliente pour appeler l’API web to-do list.

## <a name="get-an-azure-ad-b2c-directory"></a>Obtention d'un répertoire Azure AD B2C
Avant de pouvoir utiliser Azure AD B2C, vous devez créer un répertoire ou un client.  Un répertoire est un conteneur destiné à recevoir tous les utilisateurs, applications, groupes et autres.  Si vous n’en possédez pas déjà un, [créez un répertoire B2C](active-directory-b2c-get-started.md) avant de continuer.

## <a name="create-an-application"></a>Création d'une application
Vous devez maintenant créer dans votre répertoire B2C une application fournissant à Azure AD certaines informations nécessaires pour communiquer de manière sécurisée avec votre application. Dans ce cas, l’application cliente et l’API web sont toutes les deux représentées par un seul **ID d’application**, car elles constituent une application logique. Pour créer une application, suivez [ces instructions](active-directory-b2c-app-registration.md). Veillez à effectuer les opérations suivantes :

* Inclure une **application web/API web** dans l’application.
* Entrez `http://localhost/TodoListService` comme **URL de réponse**. Il s’agit de l’URL par défaut pour cet exemple de code.
* Créez une **clé secrète d’application** pour votre application et copiez-la. Vous aurez besoin de cette donnée ultérieurement. Cette valeur doit être [placée dans une séquence d’échappement XML](https://www.w3.org/TR/2006/REC-xml11-20060816/#dt-escape) avant son utilisation.
* Copiez l’ **ID d’application** affecté à votre application. Vous aurez besoin de cette donnée ultérieurement.

## <a name="create-your-policies"></a>Création de vos stratégies
Dans Azure AD B2C, chaque expérience utilisateur est définie par une [stratégie](active-directory-b2c-reference-policies.md). Cette application contient deux expériences d’identité : l’inscription et la connexion. Vous devez créer une stratégie de chaque type, comme décrit dans [l’article de référence sur les stratégies](active-directory-b2c-reference-policies.md#create-a-sign-up-policy).  Lors de la création de vos 3 stratégies, assurez-vous de :

* Choisir le **Nom d’affichage** et d’autres attributs d’inscription dans votre stratégie d’inscription.
* Choisissez le **nom d’affichage** et **l’ID objet** comme revendications d’application pour chaque stratégie.  Vous pouvez aussi choisir d'autres revendications.
* Noter le **nom** de chaque stratégie après sa création. Il doit porter le préfixe `b2c_1_`.  Vous aurez besoin des noms de ces stratégies ultérieurement.

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Une fois vos 3 stratégies créées, vous pouvez générer votre application.

Pour en savoir plus sur le fonctionnement des stratégies dans Azure AD B2C, commencez par consulter le [didacticiel de prise en main des applications web .NET](active-directory-b2c-devquickstarts-web-dotnet.md).

## <a name="download-the-code"></a>Téléchargement du code
Le code associé à ce didacticiel [est stocké sur GitHub](https://github.com/AzureADQuickStarts/B2C-WebAPI-NodeJS). Pour générer l’exemple à mesure que vous avancez, vous pouvez [télécharger une structure de projet sous la forme d’un fichier .zip](https://github.com/AzureADQuickStarts/B2C-WebAPI-NodeJS/archive/skeleton.zip). Vous pouvez également cloner la structure :

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebAPI-NodeJS.git
```

L’application terminée est également [disponible en tant que fichier .zip](https://github.com/AzureADQuickStarts/B2C-WebAPI-NodeJS/archive/complete.zip) ou sur la branche `complete` du même référentiel.

## <a name="download-nodejs-for-your-platform"></a>Télécharger Node.js pour votre plateforme
Pour utiliser cet exemple avec succès, vous avez besoin d’une installation de travail de Node.js.

Installez Node.js à partir de [nodejs.org](http://nodejs.org).

## <a name="install-mongodb-for-your-platform"></a>Installer MongoDB pour votre plateforme
Pour utiliser cet exemple avec succès, vous avez besoin d’une installation de travail de MongoDB. Nous utilisons MongoDB afin que votre API REST soit persistante sur les instances de serveur.

Installez MongoDB à partir de [mongodb.org](http://www.mongodb.org).

> [!NOTE]
> Cette procédure pas à pas suppose que vous utilisez l’installation et les points de terminaison du serveur par défaut pour MongoDB, c’est-à-dire les suivants au moment de la rédaction : `mongodb://localhost`.
>
>

## <a name="install-the-restify-modules-in-your-web-api"></a>Installer les modules Restify sur votre API web
Nous utilisons Restify pour générer votre API REST. Restify est une infrastructure d’application Node.js minimale et flexible dérivée d’Express. Elle inclut un ensemble complet de fonctionnalités de création API REST sur Connect.

### <a name="install-restify"></a>Installation de Restify
À partir de la ligne de commande, changez de répertoire pour le définir sur `azuread`. Si le répertoire `azuread` n’existe pas, créez-le.

`cd azuread` ou `mkdir azuread;`

Entrez la commande suivante :

`npm install restify`

Cette commande permet d’installer Restify.

#### <a name="did-you-get-an-error"></a>Une erreur est-elle survenue ?
Dans certains systèmes d’exploitation, lorsque vous utilisez `npm`, vous pouvez recevoir l’erreur `Error: EPERM, chmod '/usr/local/bin/..'`, ainsi qu’une requête vous demandant d’exécuter le compte en tant qu’administrateur. Si ce problème se produit, utilisez la commande `sudo` pour exécuter `npm` avec des privilèges plus élevés.

#### <a name="did-you-get-a-dtrace-error"></a>Avez-vous reçu une erreur DTrace ?
Lors de l’installation de Restify, un texte semblable au suivant peut s’afficher :

```Shell
clang: error: no such file or directory: 'HD/azuread/node_modules/restify/node_modules/dtrace-provider/libusdt'
make: *** [Release/DTraceProviderBindings.node] Error 1
gyp ERR! build error
gyp ERR! stack Error: `make` failed with exit code: 2
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

Restify fournit un mécanisme puissant pour suivre les appels REST à l’aide de DTrace. Toutefois, de nombreux systèmes d’exploitation ne disposent pas de DTrace. Vous pouvez ignorer ces erreurs.

La sortie de la commande doit ressembler au texte suivant :

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
    └── bunyan@0.22.0 (mv@0.0.5)

## <a name="install-passport-in-your-web-api"></a>Installer Passport.dans votre API web
À partir de la ligne de commande, changez de répertoire pour le définir sur `azuread`, si ce n’est pas déjà fait.

Installez Passport à l’aide de la commande suivante :

`npm install passport`

La sortie de la commande doit ressembler au texte suivant :

    passport@0.1.17 node_modules\passport
    ├── pause@0.0.1
    └── pkginfo@0.2.3

## <a name="add-passport-azuread-to-your-web-api"></a>Ajouter passport-azuread à votre API web
Ajoutez alors la stratégie OAuth à l’aide de `passport-azuread`, une suite de stratégies qui connectent Azure AD à Passport. Utilisez cette stratégie pour les jetons du porteur dans cet exemple d’API Rest.

> [!NOTE]
> Bien qu’OAuth2 fournisse une infrastructure dans laquelle tout type de jeton connu peut être émis, seuls certains types de jeton sont utilisés de manière généralisée. Les jetons protégeant les points de terminaison sont des jetons du porteur. Il s’agit des types de jeton les plus fréquemment émis dans OAuth2. De nombreuses implémentations partent du principe qu’il s’agit du seul type de jeton émis.
>
>

À partir de la ligne de commande, changez de répertoire pour le définir sur `azuread`, si ce n’est pas déjà fait.

Installez le module Passport `passport-azure-ad` à l’aide de la commande suivante :

`npm install passport-azure-ad`

La sortie de la commande doit ressembler au texte suivant :

``
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
``

## <a name="add-mongodb-modules-to-your-web-api"></a>Ajouter des modules MongoDB à votre API web
Cet exemple utilise MongoDB comme magasin de données. Pour ce dernier, installez Mongoose, un plug-in largement utilisé pour la gestion des modèles et des schémas.

* `npm install mongoose`

## <a name="install-additional-modules"></a>Installation de modules supplémentaires
Ensuite, installez les autres modules requis.

À partir de la ligne de commande, changez de répertoire pour le définir sur `azuread`, si ce n’est pas déjà fait :

`cd azuread`

Installez les modules dans votre répertoire `node_modules` :

* `npm install assert-plus`
* `npm install ejs`
* `npm install ejs-locals`
* `npm install express`
* `npm install bunyan`

## <a name="create-a-serverjs-file-with-your-dependencies"></a>Créer un fichier server.js avec vos dépendances
Le fichier `server.js` fournit la plupart des fonctionnalités de votre serveur d’API web.

À partir de la ligne de commande, changez de répertoire pour le définir sur `azuread`, si ce n’est pas déjà fait :

`cd azuread`

Créez un fichier `server.js` dans un éditeur. Ajoutez les informations suivantes :

```Javascript
'use strict';
/**
* Module dependencies.
*/
var fs = require('fs');
var path = require('path');
var util = require('util');
var assert = require('assert-plus');
var mongoose = require('mongoose/');
var bunyan = require('bunyan');
var restify = require('restify');
var config = require('./config');
var passport = require('passport');
var OIDCBearerStrategy = require('passport-azure-ad').BearerStrategy;
```

Enregistrez le fichier . Vous y reviendrez ultérieurement.

## <a name="create-a-configjs-file-to-store-your-azure-ad-settings"></a>Créer un fichier .js de configuration pour stocker vos paramètres Azure AD
Ce fichier de code transmet les paramètres de configuration de votre portail Azure AD au fichier `Passport.js`. Vous avez créé ces valeurs de configuration lorsque vous avez ajouté l’API web au portail, dans la première partie de la procédure détaillée. Copiez le code. Après quoi, nous vous expliquerons ce qu’il convient de placer dans les valeurs de ces paramètres.

À partir de la ligne de commande, changez de répertoire pour le définir sur `azuread`, si ce n’est pas déjà fait :

`cd azuread`

Créez un fichier `config.js` dans un éditeur. Ajoutez les informations suivantes :

```Javascript
// Don't commit this file to your public repos. This config is for first-run
exports.creds = {
clientID: <your client ID for this Web API you created in the portal>
mongoose_auth_local: 'mongodb://localhost/tasklist', // Your mongo auth uri goes here
audience: '<your audience URI>', // the Client ID of the application that is calling your API, usually a web API or native client
identityMetadata: 'https://login.microsoftonline.com/<tenant name>/.well-known/openid-configuration', // Make sure you add the B2C tenant name in the <tenant name> area
tenantName:'<tenant name>',
policyName:'b2c_1_<sign in policy name>' // This is the policy you'll want to validate against in B2C. Usually this is your Sign-in policy (as users sign in to this API)
passReqToCallback: false // This is a node.js construct that lets you pass the req all the way back to any upstream caller. We turn this off as there is no upstream caller.
};

```

[!INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

### <a name="required-values"></a>Valeurs requises
`clientID` : ID client de votre application API web.

`IdentityMetadata` : c’est ici que `passport-azure-ad` recherche vos données de configuration pour le fournisseur d’identité, ainsi que les clés pour valider les jetons web JSON.

`audience`: URI du portail qui identifie votre application appelante.

`tenantName`: nom de votre locataire (par exemple, **contoso.onmicrosoft.com**).

`policyName`: stratégie que vous souhaitez utiliser pour valider les jetons atteignant votre serveur. Cette stratégie doit être la même que celle que vous utilisez sur l’application cliente pour vous connecter.

> [!NOTE]
> Pour l’instant, utilisez les mêmes stratégies sur les installations client et serveur. Si vous avez déjà suivi une procédure pas à pas au cours de laquelle vous avez créé ces stratégies, vous n’avez pas besoin de répéter cette opération. Puisque vous avez suivi cette procédure, vous n’avez pas besoin de configurer de nouvelles stratégies pour les procédures pas à pas destinées au client sur le site.
>
>

## <a name="add-configuration-to-your-serverjs-file"></a>Ajouter des paramètres de configuration dans votre fichier server.js
Pour lire les valeurs du fichier `config.js` que vous avez créé, ajoutez le fichier `.config` en tant que ressource requise dans votre application, puis définissez les variables globales sur celles utilisées dans le document `config.js`.

À partir de la ligne de commande, changez de répertoire pour le définir sur `azuread`, si ce n’est pas déjà fait :

`cd azuread`

Ouvrez le fichier `server.js` dans un éditeur. Ajoutez les informations suivantes :

```Javascript
var config = require('./config');
```
Ajoutez une nouvelle section à `server.js` qui inclut le code suivant :

```Javascript
// We pass these options in to the ODICBearerStrategy.

var options = {
    // The URL of the metadata document for your app. We put the keys for token validation from the URL found in the jwks_uri tag of the in the metadata.
    identityMetadata: config.creds.identityMetadata,
    clientID: config.creds.clientID,
    tenantName: config.creds.tenantName,
    policyName: config.creds.policyName,
    validateIssuer: config.creds.validateIssuer,
    audience: config.creds.audience,
    passReqToCallback: config.creds.passReqToCallback

};
```

À présent, nous allons ajouter des espaces réservés pour les utilisateurs que nous recevons de nos applications appelantes.

```Javascript
// array to hold logged in users and the current logged in user (owner)
var users = [];
var owner = null;
```

Nous allons également créer notre enregistreur d’événements.

```Javascript
// Our logger
var log = bunyan.createLogger({
    name: 'Microsoft Azure Active Directory Sample'
});
```

## <a name="add-the-mongodb-model-and-schema-information-by-using-mongoose"></a>Ajouter les informations du modèle et du schéma MongoDB à l’aide de Moongoose
La préparation anticipée porte ses fruits lorsque vous rassemblez ces trois fichiers dans un service API REST.

Dans le cadre de cette procédure pas à pas, utilisez MongoDB pour stocker vos tâches, comme indiqué précédemment.

Dans le fichier `config.js` , vous avez appelé votre base de données **tasklist**. Ce nom est également celui qui figure à la fin de l’URL de connexion `mongoose_auth_local` . Vous n’avez pas besoin de créer cette base de données au préalable dans MongoDB. Cette base de données est créée pour vous lors de la première exécution de votre application serveur.

Après avoir indiqué au serveur quelle base de données MongoDB utiliser, vous devez écrire du code supplémentaire pour créer le modèle et le schéma pour les tâches du serveur.

### <a name="expand-the-model"></a>Développer le modèle
Ce modèle de schéma est simple. Vous pouvez le développer en fonction de vos besoins.

`owner`: personne à qui la tâche est affectée. Cet objet est une **chaîne**.  

`Text`: tâche proprement dite. Cet objet est une **chaîne**.

`date`: date d’échéance de la tâche. Cet objet est une **dateHeure**.

`completed`: si la tâche est terminée. Cet objet est un **booléen**.

### <a name="create-the-schema-in-the-code"></a>Créer le schéma dans le code
À partir de la ligne de commande, changez de répertoire pour le définir sur `azuread`, si ce n’est pas déjà fait :

`cd azuread`

Ouvrez le fichier `server.js` dans un éditeur. Ajoutez les informations suivantes sous l’entrée de configuration :

```Javascript
// MongoDB setup
// Setup some configuration
var serverPort = process.env.PORT || 3000; // Note we are hosting our API on port 3000
var serverURI = (process.env.PORT) ? config.creds.mongoose_auth_mongohq : config.creds.mongoose_auth_local;

// Connect to MongoDB
global.db = mongoose.connect(serverURI);
var Schema = mongoose.Schema;
log.info('MongoDB Schema loaded');

// Here we create a schema to store our tasks and users. Pretty simple schema for now.
var TaskSchema = new Schema({
    owner: String,
    Text: String,
    completed: Boolean,
    date: Date
});

// Use the schema to register a model
mongoose.model('Task', TaskSchema);
var Task = mongoose.model('Task');
```
Commencez par créer le schéma, puis créez un objet de modèle que vous utiliserez pour stocker vos données dans le code lorsque vous définirez vos **itinéraires**.

## <a name="add-routes-for-your-rest-api-task-server"></a>Ajouter des itinéraires pour votre serveur de la tâche API REST
Maintenant que vous disposez d’un modèle de base de données, ajoutez les itinéraires que vous utiliserez pour le serveur d’API REST.

### <a name="about-routes-in-restify"></a>À propos des itinéraires dans Restify
Dans Restify, les itinéraires fonctionnent de la même façon que lorsqu’ils utilisent la pile Express. Vous définissez des itinéraires à l’aide de l’URI qui, selon vous, sera appelé par les applications clientes.

Voici un exemple de modèle d’un itinéraire Restify :

```Javascript
function createObject(req, res, next) {
// do work on Object
_object.name = req.params.object; // passed value is in req.params under object
///...
return next(); // keep the server going
}
....
server.post('/service/:add/:object', createObject); // calls createObject on routes that match this.
```

Restify et Express fournissent des fonctionnalités bien plus approfondies, comme la définition de types d’application et la création d’un routage complexe entre différents points de terminaison. Pour les besoins de ce didacticiel, nous utilisons des itinéraires simples.

#### <a name="add-default-routes-to-your-server"></a>Ajouter des itinéraires par défaut à votre serveur
À présent, vous allez ajouter les itinéraires CRUD de base de **création** et de **liste** pour notre API REST. D’autres itinéraires se trouvent dans la branche `complete` de l’exemple.

À partir de la ligne de commande, changez de répertoire pour le définir sur `azuread`, si ce n’est pas déjà fait :

`cd azuread`

Ouvrez le fichier `server.js` dans un éditeur. Ajoutez les informations suivantes au-dessous des entrées de base de données créées précédemment :

```Javascript
/**
 *
 * APIs for our REST Task server
 */

// Create a task

function createTask(req, res, next) {

    // Resitify currently has a bug which doesn't allow you to set default headers
    // This headers comply with CORS and allow us to mongodbServer our response to any origin

    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "X-Requested-With");

    // Create a new task model, fill it up and save it to Mongodb
    var _task = new Task();

    if (!req.params.Text) {
        req.log.warn({
            params: req.params
        }, 'createTodo: missing task');
        next(new MissingTaskError());
        return;
    }

    _task.owner = owner;
    _task.Text = req.params.Text;
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
```

```Javascript
/// Simple returns the list of TODOs that were loaded.

function listTasks(req, res, next) {
    // Resitify currently has a bug which doesn't allow you to set default headers
    // This headers comply with CORS and allow us to mongodbServer our response to any origin

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
            log.warn(err, "There is no tasks in the database. Add one!");
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


#### <a name="add-error-handling-for-the-routes"></a>Ajouter la gestion des erreurs pour les itinéraires
Ajoutez la gestion des erreurs afin de pouvoir rapporter de manière intelligible au client les problèmes que vous rencontrez.

Ajoutez le code suivant :

```Javascript
///--- Errors for communicating something interesting back to the client
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


## <a name="create-your-server"></a>Créer votre serveur
Vous avez maintenant défini votre base de données et mis vos itinéraires en place. Pour finir, ajoutez l’instance de serveur qui gérera vos appels.

Restify et Express permettent la personnalisation complète d’un serveur d’API REST, mais nous utilisons ici la configuration la plus simple.

```Javascript

/**
 * Our Server
 */


var server = restify.createServer({
    name: "Microsoft Azure Active Directroy TODO Server",
    version: "2.0.1"
});

// Ensure we don't drop data on uploads
server.pre(restify.pre.pause());

// Clean up sloppy paths like //todo//////1//
server.pre(restify.pre.sanitizePath());

// Handles annoying user agents (curl)
server.pre(restify.pre.userAgentConnection());

// Set a per request bunyan logger (with requestid filled in)
server.use(restify.requestLogger());

// Allow 5 requests/second by IP, and burst to 10
server.use(restify.throttle({
    burst: 10,
    rate: 5,
    ip: true,
}));

// Use the common stuff you probably want
server.use(restify.acceptParser(server.acceptable));
server.use(restify.dateParser());
server.use(restify.queryParser());
server.use(restify.gzipResponse());
server.use(restify.bodyParser({
    mapParams: true
})); // Allows for JSON mapping to REST
server.use(restify.authorizationParser()); // Looks for authorization headers

// Let's start using Passport.js

server.use(passport.initialize()); // Starts passport
server.use(passport.session()); // Provides session support


```
## <a name="add-the-routes-to-the-server-without-authentication"></a>Ajouter les itinéraires au serveur (sans authentification)
```Javascript
server.get('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), listTasks);
server.get('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), listTasks);
server.get('/api/tasks/:owner', passport.authenticate('oauth-bearer', {
    session: false
}), getTask);
server.head('/api/tasks/:owner', passport.authenticate('oauth-bearer', {
    session: false
}), getTask);
server.post('/api/tasks/:owner/:task', passport.authenticate('oauth-bearer', {
    session: false
}), createTask);
server.post('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), createTask);
server.del('/api/tasks/:owner/:task', passport.authenticate('oauth-bearer', {
    session: false
}), removeTask);
server.del('/api/tasks/:owner', passport.authenticate('oauth-bearer', {
    session: false
}), removeTask);
server.del('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), removeTask);
server.del('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), removeAll, function respond(req, res, next) {
    res.send(204);
    next();
});


// Register a default '/' handler

server.get('/', function root(req, res, next) {
    var routes = [
        'GET     /',
        'POST    /api/tasks/:owner/:task',
        'POST    /api/tasks (for JSON body)',
        'GET     /api/tasks',
        'PUT     /api/tasks/:owner',
        'GET     /api/tasks/:owner',
        'DELETE  /api/tasks/:owner/:task'
    ];
    res.send(200, routes);
    next();
});
```

```Javascript

server.listen(serverPort, function() {

    var consoleMessage = '\n Microsoft Azure Active Directory Tutorial';
    consoleMessage += '\n +++++++++++++++++++++++++++++++++++++++++++++++++++++';
    consoleMessage += '\n %s server is listening at %s';
    consoleMessage += '\n Open your browser to %s/api/tasks\n';
    consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n';
    consoleMessage += '\n !!! why not try a $curl -isS %s | json to get some ideas? \n';
    consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n\n';

    //log.info(consoleMessage, server.name, server.url, server.url, server.url);

});

```

## <a name="add-authentication-to-your-rest-api-server"></a>Ajouter une authentification à votre serveur API REST
Maintenant que vous disposez d’un serveur API REST en cours d’exécution, vous pouvez l’utiliser dans Azure AD.

À partir de la ligne de commande, changez de répertoire pour le définir sur `azuread`, si ce n’est pas déjà fait :

`cd azuread`

### <a name="use-the-oidcbearerstrategy-that-is-included-with-passport-azure-ad"></a>Utiliser la stratégie OIDCBearerStrategy incluse avec passport-azure-ad
> [!TIP]
> Lors de l’écriture d’API, vous devez toujours lier les données à un élément unique du jeton, dont un utilisateur ne peut pas usurper l’identité. Lorsque le serveur stocke des tâches, il le fait en fonction de **l’OID** de l’utilisateur dans le jeton (appelé via token.oid) placé dans le champ « propriétaire ». Cette valeur permet de s’assurer que seul cet utilisateur peut accéder à ses propres tâches. Il n’y a pas d’exposition dans l’API du « propriétaire », afin qu’un utilisateur externe puisse demander les ToDo d’un tiers même s’ils sont authentifiés.
>
>

Maintenant, utilisez la stratégie du porteur fournie avec `passport-azure-ad`.

```Javascript
var findById = function(id, fn) {
    for (var i = 0, len = users.length; i < len; i++) {
        var user = users[i];
        if (user.oid === id) {
            log.info('Found user: ', user);
            return fn(null, user);
        }
    }
    return fn(null, null);
};


var oidcStrategy = new OIDCBearerStrategy(options,
    function(token, done) {
        log.info('verifying the user');
        log.info(token, 'was the token retreived');
        findById(token.sub, function(err, user) {
            if (err) {
                return done(err);
            }
            if (!user) {
                // "Auto-registration"
                log.info('User was added automatically as they were new. Their sub is: ', token.oid);
                users.push(token);
                owner = token.oid;
                return done(null, token);
            }
            owner = token.sub;
            return done(null, user, token);
        });
    }
);

passport.use(oidcStrategy);
```

Passport utilise le même modèle pour toutes ses stratégies. Vous lui transmettez une `function()` qui dispose des paramètres `token` et `done`. La stratégie vous revient après avoir effectué toutes les tâches. Vous devez ensuite stocker l’utilisateur et enregistrer le jeton pour ne pas avoir à le demander de nouveau.

> [!IMPORTANT]
> Le code ci-dessus note tout utilisateur s’authentifiant sur votre serveur. Ce processus est appelé enregistrement automatique. Dans les serveurs de production, ne laissez pas les utilisateurs accéder à l’API sans les avoir au préalable soumis à un processus d’inscription. Ce processus est généralement le modèle des applications consommateur qui vous permettent de vous inscrire via Facebook, mais vous demandent ensuite de renseigner des informations supplémentaires. S’il ne s’agissait pas d’un programme de ligne de commande, nous aurions pu extraire l’adresse de messagerie de l’objet de jeton retourné, avant d’inviter les utilisateurs à entrer des informations supplémentaires. Comme il s’agit d’un exemple, nous les ajoutons à une base de données en mémoire.
>
>

## <a name="run-your-server-application-to-verify-that-it-rejects-you"></a>Exécuter votre application serveur et vous assurer de votre rejet
Vous pouvez utiliser `curl` pour voir si vous disposez à présent de la protection OAuth2 sur vos points de terminaison. Les en-têtes retournés doivent suffire pour vous indiquer que vous êtes sur la bonne voie.

Vérifiez que votre instance MongoDB est en cours d’exécution :

    $sudo mongodb

Passez au répertoire et exécutez le serveur :

    $ cd azuread
    $ node server.js

Dans une nouvelle fenêtre de terminal, exécutez `curl`

Essayez une commande basique, comme POST :

`$ curl -isS -X POST http://127.0.0.1:3000/api/tasks/brandon/Hello`

```Shell
HTTP/1.1 401 Unauthorized
Connection: close
WWW-Authenticate: Bearer realm="Users"
Date: Tue, 14 Jul 2015 05:45:03 GMT
Transfer-Encoding: chunked
```

Une erreur 401 est la réponse souhaitée. Elle indique que la couche Passport tente de rediriger vers le point de terminaison d’autorisation.

## <a name="you-now-have-a-rest-api-service-that-uses-oauth2"></a>Vous disposez maintenant d’un service API REST qui utilise OAuth2.
Vous avez implémenté une API REST à l’aide de Restify et d’OAuth. Vous disposez maintenant de suffisamment de code pour pouvoir continuer à développer votre service et à vous appuyer sur cet exemple. Vous êtes allé aussi loin que possible avec ce serveur sans utiliser un client compatible OAuth2. Pour cette étape suivante, utilisez une autre procédure pas à pas comme celle-ci : [Se connecter à une API web en utilisant iOS avec B2C](active-directory-b2c-devquickstarts-ios.md) .

## <a name="next-steps"></a>Étapes suivantes
Vous pouvez maintenant aborder des rubriques plus avancées, telles que :

[Se connecter à une API web en utilisant iOS avec B2C](active-directory-b2c-devquickstarts-ios.md)
