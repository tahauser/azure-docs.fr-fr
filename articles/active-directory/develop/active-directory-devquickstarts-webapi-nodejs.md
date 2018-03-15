---
title: "Bien démarrer avec l’API web Azure AD Node.js | Microsoft Docs"
description: "Création d’une API web REST Node.js qui s’intègre à Azure AD pour l’authentification."
services: active-directory
documentationcenter: nodejs
author: craigshoemaker
manager: mtillman
ms.assetid: 7654ab4c-4489-4ea5-aba9-d7cdc256e42a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 11/30/2017
ms.author: cshoe
ms.custom: aaddev
ms.openlocfilehash: 2ed0874b79601976e0d5a73fe82c7c03331d9bea
ms.sourcegitcommit: 828cd4b47fbd7d7d620fbb93a592559256f9d234
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/18/2018
---
# <a name="azure-ad-nodejs-web-api-getting-started"></a>Bien démarrer avec l’API web Azure AD Node.js

Cet article explique comment sécuriser un point de terminaison d’API [Restify](http://restify.com/) avec [Passport](http://passportjs.org/) à l’aide du module [passport-azure-ad](https://github.com/AzureAD/passport-azure-ad) pour gérer les communications avec Azure Active Directory (AAD). 

Ce didacticiel couvre tous les aspects relatifs à la sécurisation des points de terminaison d’API. Les questions de connexion et de conservation des jetons d’authentification ne sont pas abordées ici et sont la responsabilité des applications clientes. Pour plus d’informations sur l’implémentation du client, consultez [Connexion et déconnexion d’Azure AD à l’aide d’une application web Node.js](active-directory-devquickstarts-openidconnect-nodejs.md).

L’exemple de code complet associé à cet article est disponible sur [GitHub](https://github.com/Azure-Samples/active-directory-node-webapi-basic).

## <a name="create-the-sample-project"></a>Créer l’exemple de projet
Cette application serveur nécessite que certaines dépendances de package prennent en charge Restify et Passport, ainsi que les informations de compte qui sont transmises à AAD.

Pour commencer, ajoutez le code suivant dans un fichier nommé `package.json` :

```Shell
{
  "name": "node-aad-demo",
  "version": "0.0.1",
  "scripts": {
    "start": "node app.js"
  },
  "dependencies": {
    "passport": "0.4.0",
    "passport-azure-ad": "3.0.8",
    "restify": "6.0.1",
    "restify-plugins": "1.6.0"
  }
}
```

Une fois que `package.json` est créé, exécutez `npm install` dans votre invite de commandes pour installer les dépendances du package. 

### <a name="configure-the-project-to-use-active-directory"></a>Configurer le projet de manière à utiliser Active Directory

Commencez la configuration de l’application en récupérant d’Azure CLI certaines valeurs spécifiques des comptes. Pour commencer avec l’interface CLI, le plus simple est d’utiliser Azure Cloud Shell.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Entrez la commande suivante dans Cloud Shell : 

```azurecli-interactive
az ad app create --display-name node-aad-demo --homepage http://localhost --identifier-uris http://node-aad-demo
```

Les [arguments](/cli/azure/ad/app?view=azure-cli-latest#az_ad_app_create) de la commande `create` comprennent les éléments suivants :

| Argument  | Description |
|---------|---------|
|`display-name` | Le nom convivial de l’inscription |
|`homepage` | L’URL à laquelle les utilisateurs peuvent se connecter et utiliser votre application |
|`identifier-uris` | Les URI uniques, séparés par un espace, qu’Azure AD peut utiliser pour cette application |

Avant de vous connecter à Azure Active Directory, vous avez besoin des informations suivantes :

| NOM  | Description | Nom de la variable dans le fichier config |
| ------------- | ------------- | ------------- |
| Nom du locataire  | [Nom de locataire](active-directory-howto-tenant.md) que vous souhaitez utiliser pour l’authentification | `tenantName`  |
| ID client  | « ID de client » est un terme OAuth qui correspond à _l’ID d’application_ AAD. |  `clientID`  |

À partir de la réponse de l’inscription dans Azure Cloud Shell, copiez la valeur `appId` et créez un fichier nommé `config.js`. Ensuite, ajoutez le code suivant, puis remplacez vos valeurs par les jetons entre crochets :

```JavaScript
const tenantName    = //<YOUR_TENANT_NAME>;
const clientID      = //<YOUR_APP_ID_FROM_CLOUD_SHELL>;
const serverPort    = 3000;

module.exports.serverPort = serverPort;

module.exports.credentials = {
  identityMetadata: `https://login.microsoftonline.com/${tenantName}.onmicrosoft.com/.well-known/openid-configuration`, 
  clientID: clientID
};
```
Pour plus d’informations sur les paramètres de configuration, consultez la documentation du module [passport-azure-ad](https://github.com/AzureAD/passport-azure-ad#5-usage).

## <a name="implement-the-server"></a>Implémenter le serveur
Le module [passport-azure-ad](https://github.com/AzureAD/passport-azure-ad#5-usage) comporte deux stratégies d’authentification : par [OIDC](https://github.com/AzureAD/passport-azure-ad#51-oidcstrategy) et par [Porteur](https://github.com/AzureAD/passport-azure-ad#52-bearerstrategy). Le serveur implémenté dans cet article utilise la stratégie par porteur pour sécuriser le point de terminaison d’API.

### <a name="step-1-import-dependencies"></a>Étape 1 : Importer les dépendances
Créez un fichier nommé `app.js`, puis collez-y le texte suivant :

```JavaScript
const
      restify = require('restify')
    , restifyPlugins = require('restify-plugins')
    , passport = require('passport')
    , BearerStrategy = require('passport-azure-ad').BearerStrategy
    , config = require('./config')
    , authenticatedUserTokens = []
    , serverPort = process.env.PORT || config.serverPort
;
```

Dans cette section de code :

- Les modules `restify` et `restify-plugins` sont référencés afin de configurer un serveur Restify.

- Les modules `passport` et `passport-azure-ad` sont responsables de la communication avec AAD.

- La variable `config` est initialisée avec les valeurs du fichier `config.js` créé à l’étape précédente.

- Un tableau est créé pour `authenticatedUserTokens` afin de stocker les jetons utilisateur lorsqu’ils sont passés dans les points de terminaison sécurisés.

- `serverPort` est défini à partir du port de l’environnement de processus ou à partir du fichier de configuration.

### <a name="step-2-instantiate-an-authentication-strategy"></a>Étape 2 : Instancier une stratégie d’authentification
Lorsque vous sécurisez un point de terminaison, vous devez fournir une stratégie chargée de déterminer si la requête actuelle provient d’un utilisateur authentifié. Ici, la variable `authenticatonStrategy` est une instance de la classe `passport-azure-ad` `BearerStrategy`. Ajoutez le code suivant après les instructions `require`.

```JavaScript
const authenticationStrategy = new BearerStrategy(config.credentials, (token, done) => {
    let currentUser = null;

    let userToken = authenticatedUserTokens.find((user) => {
        currentUser = user;
        user.sub === token.sub;
    });

    if(!userToken) {
        authenticatedUserTokens.push(token);
    }

    return done(null, currentUser, token);
});
```
Cette implémentation utilise l’enregistrement automatique en ajoutant des jetons d’authentification au tableau `authenticatedUserTokens`, si ceux-ci ne s’y trouvent pas déjà.

Lorsqu’une nouvelle instance de la stratégie est créée, vous devez la passer à Passport via la méthode `use`. Ajoutez le code suivant à `app.js` pour utiliser la stratégie dans Passport.

```JavaScript
passport.use(authenticationStrategy);
```

### <a name="step-3-server-configuration"></a>Étape 3 : Configuration du serveur
Une fois la stratégie d’authentification définie, vous pouvez configurer le serveur Restify avec certains paramètres de base et le configurer de manière à utiliser Passport pour la sécurité.

```JavaScript
const server = restify.createServer({ name: 'Azure Active Directroy with Node.js Demo' });
server.use(restifyPlugins.authorizationParser());
server.use(passport.initialize());
server.use(passport.session());
```
Ce serveur est initialisé et configuré pour analyser les en-têtes d’autorisation, puis configuré pour utiliser Passport.


### <a name="step-4-define-routes"></a>Étape 4 : Définir des itinéraires
Vous pouvez maintenant définir des itinéraires et décider de ceux que vous souhaitez sécuriser avec AAD. Ce projet comprend deux itinéraires pour lesquels le niveau racine est ouvert et l’itinéraire `/api` est défini pour exiger une authentification.

Dans `app.js`, ajoutez le code suivant pour l’itinéraire de niveau racine :

```JavaScript
server.get('/', (req, res, next) => {
    res.send(200, 'Try: curl -isS -X GET http://127.0.0.1:3000/api');
    next();
});
```

L’itinéraire racine autorise toutes les demandes qui passent par l’itinéraire et retourne un message comprenant une commande pour tester l’itinéraire `/api`. En revanche, l’itinéraire `/api` est verrouillé à l’aide de [`passport.authenticate`](http://passportjs.org/docs/authenticate). Ajoutez le code suivant après l’itinéraire racine.

```JavaScript
server.get('/api', passport.authenticate('oauth-bearer', { session: false }), (req, res, next) => {
    res.json({ message: 'response from API endpoint' });
    return next();
});
```

Cette configuration autorise uniquement les demandes authentifiées qui incluent un accès par jeton du porteur à `/api`. L’option de `session: false` est utilisée pour désactiver des sessions dans le but d’exiger qu’un jeton soit passé à chaque requête envoyée à l’API.

Enfin, le serveur est configuré pour écouter le port configuré en appelant la méthode `listen`.

```JavaScript
server.listen(serverPort);
```

## <a name="run-the-sample"></a>Exécution de l'exemple

Maintenant que le serveur est implémenté, vous pouvez démarrer le serveur en ouvrant une invite de commandes et en entrant ce qui suit :

```Shell
npm start
```

Lorsque le serveur est en cours d’exécution, vous pouvez lui envoyer une demande pour tester les résultats. Pour démontrer la réponse de l’itinéraire racine, ouvrez l’interpréteur de commandes Bash et entrez le code suivant :

```Shell 
curl -isS -X GET http://127.0.0.1:3000/
```

Si vous avez correctement configuré votre serveur, la réponse doit ressembler à ceci :

```Shell
HTTP/1.1 200 OK
Server: Azure Active Directroy with Node.js Demo
Content-Type: application/json
Content-Length: 49
Date: Tue, 10 Oct 2017 18:35:13 GMT
Connection: keep-alive

Try: curl -isS -X GET http://127.0.0.1:3000/api
```

Ensuite, vous pouvez tester l’itinéraire qui nécessite une authentification en entrant la commande suivante dans votre interpréteur de commandes Bash :

```Shell 
curl -isS -X GET http://127.0.0.1:3000/api
```

Si vous avez correctement configuré le serveur, celui-ci doit répondre en affichant l’état `Unauthorized`.

```Shell
HTTP/1.1 401 Unauthorized
Server: Azure Active Directroy with Node.js Demo
WWW-Authenticate: token is not found
Date: Tue, 10 Oct 2017 16:22:03 GMT
Connection: keep-alive
Content-Length: 12

Unauthorized
```
Maintenant que vous avez créé une API sécurisée, vous pouvez implémenter un client capable de passer des jetons d’authentification à l’API.

## <a name="next-steps"></a>Étapes suivantes
Comme indiqué dans l’introduction, vous devez implémenter un homologue client pour vous connecter au serveur chargé de la connexion, de la déconnexion et de la gestion des jetons. Pour obtenir des exemples de code, vous pouvez vous référer aux applications clientes [iOS](https://github.com/MSOpenTech/azure-activedirectory-library-for-ios) et [Android](https://github.com/MSOpenTech/azure-activedirectory-library-for-android). Pour obtenir un didacticiel pas à pas, reportez-vous à l’article suivant :

> [!div class="nextstepaction"]
> [Connexion et déconnexion d’Azure AD à l’aide d’une application web Node.js](active-directory-devquickstarts-openidconnect-nodejs.md)