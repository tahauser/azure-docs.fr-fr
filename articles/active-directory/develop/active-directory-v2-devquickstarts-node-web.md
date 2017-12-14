---
title: "Connexion à une application web Node.js à l’aide d’Azure Active Directory v2.0 | Microsoft Docs"
description: "Découvrez comment générer une application web Node.js qui connecte les utilisateurs à l’aide de leur compte Microsoft personnel et de leurs comptes professionnel ou scolaire."
services: active-directory
documentationcenter: nodejs
author: navyasric
manager: mtillman
editor: 
ms.assetid: 1b889e72-f5c3-464a-af57-79abf5e2e147
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 05/13/2017
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 230d8ad16dc62564f3c1149443dd59fbb9974db5
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="add-sign-in-to-a-nodejs-web-app"></a>Ajouter une connexion à une application web Node.js

> [!NOTE]
> Certains scénarios et fonctionnalités d’Azure Active Directory ne fonctionnent pas avec le point de terminaison 2.0. Pour déterminer si vous devez utiliser le point de terminaison 2.0 ou 1.0, consultez les [limites de 2.0](active-directory-v2-limitations.md).
> 

Dans ce didacticiel, nous utilisons Passport pour effectuer les tâches suivantes :

* Dans une application web, connectez l’utilisateur à l’aide d’Azure Active Directory (Azure AD) et du point de terminaison v2.0.
* afficher les informations sur l’utilisateur ;
* déconnecter l’utilisateur de l’application.

**Passport** est un intergiciel d’authentification pour Node.js. Flexible et modulaire, Passport peut être intégré de façon transparente dans n’importe quelle application web Restify ou basée sur Express. Dans Passport, une gamme complète de stratégies prend en charge l’authentification à l’aide d’un nom d’utilisateur et d’un mot de passe, de Facebook, de Twitter ou d’autres options. Nous avons développé une stratégie pour Azure AD. Dans cet article, nous vous montrons comment installer le module, puis comment ajouter le plug-in `passport-azure-ad` d’Azure AD.

## <a name="download"></a>Télécharger
Le code associé à ce didacticiel est stocké [sur GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs). Pour suivre le didacticiel, vous pouvez [télécharger la structure de l’application au format .zip](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs/archive/skeleton.zip) ou bien la cloner :

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git```

Vous pouvez également obtenir l’application terminée à la fin de ce didacticiel.

## <a name="1-register-an-app"></a>1 : Inscrire une application
Créez une application sur [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) ou suivez cette [procédure détaillée](active-directory-v2-app-registration.md) pour inscrire une application. Assurez-vous d’effectuer les tâches suivantes :

* Copier **l’ID d’application** affecté à votre application. Vous en aurez besoin pour ce didacticiel.
* Ajoutez la plate-forme **Web** pour votre application.
* Copiez **l’URI de redirection** provenant du portail. Vous devez utiliser la valeur d’URI par défaut `urn:ietf:wg:oauth:2.0:oob`.

## <a name="2-add-prerequisities-to-your-directory"></a>2 : Ajouter les éléments requis à votre répertoire
À l’invite de commande, accédez aux répertoires de votre dossier racine si ce n’est pas déjà fait. Exécutez les commandes suivantes :

* `npm install express`
* `npm install ejs`
* `npm install ejs-locals`
* `npm install restify`
* `npm install mongoose`
* `npm install bunyan`
* `npm install assert-plus`
* `npm install passport`
* `npm install webfinger`
* `npm install body-parser`
* `npm install express-session`
* `npm install cookie-parser`

En outre, nous utilisons `passport-azure-ad` dans la structure du démarrage rapide :

* `npm install passport-azure-ad`

Cela installe les bibliothèques qu’utilise `passport-azure-ad`.

## <a name="3-set-up-your-app-to-use-the-passport-node-js-strategy"></a>3. Configurer l’application pour utiliser la stratégie passport-node-js
Configurez l’intergiciel Express pour utiliser le protocole d’authentification OpenID Connect. Passport sera utilisé notamment pour émettre des demandes de connexion et de déconnexion, gérer la session utilisateur et obtenir des informations concernant l’utilisateur.

1.  Ouvrez le fichier Config.js à la racine du projet. Dans la section `exports.creds`, entrez les valeurs de configuration de votre application.
  
  * `clientID` : **l’ID d’application** que le portail Azure a affecté à votre application.
  * `returnURL` : **l’URI de redirection** que vous avez saisie dans le portail.
  * `clientSecret` : la clé secrète que vous avez générée dans le portail.

2.  Ouvrez le fichier App.js à la racine du projet. Pour appeler le stratey OIDCStrategy, qui est fournie avec `passport-azure-ad`, ajoutez l’appel suivant :

  ```JavaScript
  var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

  // Add some logging
  var log = bunyan.createLogger({
      name: 'Microsoft OIDC Example Web Application'
  });
  ```

3.  Utilisez la stratégie référencée pour gérer les demandes de connexion :

  ```JavaScript
  // Use the OIDCStrategy within Passport (section 2)
  //
  //   Strategies in Passport require a `validate` function. The function accepts
  //   credentials (in this case, an OpenID identifier), and invokes a callback
  //   with a user object.
  passport.use( new OIDCStrategy({
      callbackURL: config.creds.returnURL,
      realm: config.creds.realm,
      clientID: config.creds.clientID,
      clientSecret: config.creds.clientSecret,
      oidcIssuer: config.creds.issuer,
      identityMetadata: config.creds.identityMetadata,
      responseType: config.creds.responseType,
      responseMode: config.creds.responseMode,
      skipUserProfile: config.creds.skipUserProfile
      scope: config.creds.scope
    },
    function(iss, sub, profile, accessToken, refreshToken, done) {
      log.info('Example: Email address we received was: ', profile.email);
      // Asynchronous verification, for effect...
      process.nextTick(function () {
        findByEmail(profile.email, function(err, user) {
          if (err) {
            return done(err);
          }
          if (!user) {
            // "Auto-registration"
            users.push(profile);
            return done(null, profile);
          }
          return done(null, user);
        });
      });
    }
  ));
  ```

Passport utilise un modèle similaire pour toutes ses stratégies (Twitter, Facebook, etc.). Tous les enregistreurs de stratégie adhèrent au modèle. Transmettez à la stratégie une `function()` qui utilise un jeton et `done` comme paramètres. La stratégie est retournée une fois qu’elle a effectué tout son travail. Stockez l’utilisateur et mettez le jeton de côté pour ne pas avoir à le redemander.

  > [!IMPORTANT]
  > Le code précédent accepte n’importe quel utilisateur qui peut s’authentifier auprès de votre serveur. C’est ce qu’on appelle l’enregistrement automatique. Sur un serveur de production, vous n’allez laisser entrer personne sans d’abord lui imposer un processus d’inscription de votre choix. Il s’agit généralement du modèle que vous voyez dans les applications consommateur. L’application peut permettre de vous inscrire sur Facebook, mais elle vous demande ensuite d’entrer des informations supplémentaires. Si vous n’utilisiez pas un programme de ligne de commande pour ce didacticiel, vous pouvez extraire l’e-mail de l’objet jeton qui est retourné. Ensuite, vous pouvez demander à l’utilisateur d’entrer des informations supplémentaires. Comme il s’agit d’un serveur de test, vous ajoutez l’utilisateur directement à la base de données en mémoire.
  > 
  > 

4.  Ajoutez les méthodes que vous utilisez pour effectuer le suivi des utilisateurs qui se sont connectés, comme requis par Passport. Cela inclut la sérialisation et la désérialisation des informations d’utilisateur :

  ```JavaScript

  // Passport session setup (section 2)

  //   To support persistent login sessions, Passport needs to be able to
  //   serialize users into, and deserialize users out of, the session. Typically,
  //   this is as simple as storing the user ID when serializing, and finding
  //   the user by ID when deserializing.
  passport.serializeUser(function(user, done) {
    done(null, user.email);
  });

  passport.deserializeUser(function(id, done) {
    findByEmail(id, function (err, user) {
      done(err, user);
    });
  });

  // Array to hold signed-in users
  var users = [];

  var findByEmail = function(email, fn) {
    for (var i = 0, len = users.length; i < len; i++) {
      var user = users[i];
      log.info('we are using user: ', user);
      if (user.email === email) {
        return fn(null, user);
      }
    }
    return fn(null, null);
  };
  ```

5.  Ajoutez le code pour charger le moteur Express. Vous utiliserez le modèle /views et /routes par défaut fourni par Express :

  ```JavaScript

  // Set up Express (section 2)

  var app = express();

  app.configure(function() {
    app.set('views', __dirname + '/views');
    app.set('view engine', 'ejs');
    app.use(express.logger());
    app.use(express.methodOverride());
    app.use(cookieParser());
    app.use(expressSession({ secret: 'keyboard cat', resave: true, saveUninitialized: false }));
    app.use(bodyParser.urlencoded({ extended : true }));
    // Initialize Passport!  Also use passport.session() middleware, to support
    // persistent login sessions (recommended).
    app.use(passport.initialize());
    app.use(passport.session());
    app.use(app.router);
    app.use(express.static(__dirname + '/../../public'));
  });

  ```

6.  Ajoutez maintenant les itinéraires POST qui transmettent les demandes de connexion au moteur `passport-azure-ad` :

  ```JavaScript

  // Auth routes (section 3)

  // GET /auth/openid
  //   Use passport.authenticate() as route middleware to authenticate the
  //   request. The first step in OpenID authentication involves redirecting
  //   the user to the user's OpenID provider. After authenticating, the OpenID
  //   provider redirects the user back to this application at
  //   /auth/openid/return.

  app.get('/auth/openid',
    passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
    function(req, res) {
      log.info('Authentication was called in the sample');
      res.redirect('/');
    });

  // GET /auth/openid/return
  //   Use passport.authenticate() as route middleware to authenticate the
  //   request. If authentication fails, the user is redirected back to the
  //   sign-in page. Otherwise, the primary route function is called.
  //   In this example, it redirects the user to the home page.
  app.get('/auth/openid/return',
    passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
    function(req, res) {

      res.redirect('/');
    });

  // POST /auth/openid/return
  //   Use passport.authenticate() as route middleware to authenticate the
  //   request. If authentication fails, the user is redirected back to the
  //   sign-in page. Otherwise, the primary route function is called. 
  //   In this example, it redirects the user to the home page.

  app.post('/auth/openid/return',
    passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
    function(req, res) {

      res.redirect('/');
    });
  ```

## <a name="4-use-passport-to-issue-sign-in-and-sign-out-requests-to-azure-ad"></a>4. Utiliser Passport pour émettre des demandes de connexion et de déconnexion dans Azure AD
Votre application est maintenant configurée pour communiquer avec le point de terminaison v2.0 en utilisant le protocole d’authentification OpenID Connect. La stratégie `passport-azure-ad` a pris en charge tous les détails de la création de messages d’authentification, de la validation des jetons d’Azure AD et de la gestion des sessions utilisateur. Il ne reste plus qu’à fournir aux utilisateurs un moyen de se connecter, de se déconnecter et de collecter des informations supplémentaires une fois connectés.

1.  Ajoutez les méthodes **par défaut**, **de connexion**, **de compte** et **de déconnexion** à votre fichier App.js :

  ```JavaScript

  //Routes (section 4)

  app.get('/', function(req, res){
    res.render('index', { user: req.user });
  });

  app.get('/account', ensureAuthenticated, function(req, res){
    res.render('account', { user: req.user });
  });

  app.get('/login',
    passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
    function(req, res) {
      log.info('Login was called in the sample');
      res.redirect('/');
  });

  app.get('/logout', function(req, res){
    req.logout();
    res.redirect('/');
  });

  ```

  Voici les détails :
    
    * L’itinéraire `/` vous redirige vers la vue index.ejs. Il transfère l’utilisateur dans la demande (le cas échéant).
    * L’itinéraire `/account` s’assure d’abord *que vous vous êtes authentifié* (à implémenter dans le code suivant). Il transfère ensuite l’utilisateur dans la demande. Vous pourrez ainsi obtenir des informations supplémentaires sur l’utilisateur.
    * L’itinéraire `/login` appelle votre application d’authentification `azuread-openidconnect` depuis `passport-azuread`. En cas d’échec, il redirige l’utilisateur vers `/login`.
    * L’itinéraire `/logout` appelle la vue (et l’itinéraire) logout.ejs. Cela efface les cookies, puis renvoie l’utilisateur vers index.ejs.

2.  Ajoutez la méthode **EnsureAuthenticated** que vous avez utilisée précédemment dans `/account` :

  ```JavaScript

  // Route middleware to ensure the user is authenticated (section 4)

  //   Use this route middleware on any resource that needs to be protected. If
  //   the request is authenticated (typically via a persistent login session),
  //   the request proceeds. Otherwise, the user is redirected to the
  //   sign-in page.
  function ensureAuthenticated(req, res, next) {
    if (req.isAuthenticated()) { return next(); }
    res.redirect('/login')
  }

  ```

3.  Dans App.js, créez le serveur :

  ```JavaScript

  app.listen(3000);

  ```


## <a name="5-create-the-views-and-routes-in-express-that-you-show-your-user-on-the-website"></a>5. Créer des vues et des itinéraires dans Express pour afficher l’utilisateur dans le site web
Ajoutez les itinéraires et les vues qui fournissent des informations à l’utilisateur. Les itinéraires et les vues gèrent également les itinéraires `/logout` et `/login` que vous avez créés.

1. Créez l’itinéraire `/routes/index.js` dans le répertoire racine.

  ```JavaScript

  /*
  * GET home page.
  */

  exports.index = function(req, res){
    res.render('index', { title: 'Express' });
  };
  ```

2.  Créez l’itinéraire `/routes/user.js` dans le répertoire racine.

  ```JavaScript

  /*
  * GET users listing.
  */

  exports.list = function(req, res){
    res.send("respond with a resource");
  };
  ```

  `/routes/index.js` et `/routes/user.js` sont des itinéraires simples qui transmettent la demande à vos vues, en incluant l’utilisateur le cas échéant.

3.  Créez la vue `/views/index.ejs` dans le répertoire racine. Cette page appelle vos méthodes **de connexion** et **de déconnexion**. Vous utiliserez également la vue `/views/index.ejs` pour capturer les informations de compte. Vous pouvez utiliser la condition `if (!user)` lorsque l’utilisateur est transféré dans la demande. Cela signifie que vous disposez d’un utilisateur connecté.

  ```JavaScript
  <% if (!user) { %>
      <h2>Welcome! Please sign in.</h2>
      <a href="/login">Sign in</a>
  <% } else { %>
      <h2>Hello, <%= user.displayName %>.</h2>
      <a href="/account">Account info</a></br>
      <a href="/logout">Sign out</a>
  <% } %>
  ```

4.  Créez la vue `/views/account.ejs` dans le répertoire racine. La vue `/views/account.ejs` vous permet d’afficher des informations supplémentaires que `passport-azuread` ajoute à la demande de l’utilisateur.

  ```Javascript
  <% if (!user) { %>
      <h2>Welcome! Please sign in.</h2>
      <a href="/login">Sign in</a>
  <% } else { %>
  <p>displayName: <%= user.displayName %></p>
  <p>givenName: <%= user.name.givenName %></p>
  <p>familyName: <%= user.name.familyName %></p>
  <p>UPN: <%= user._json.upn %></p>
  <p>Profile ID: <%= user.id %></p>
  <p>Full Claimes</p>
  <%- JSON.stringify(user) %>
  <p></p>
  <a href="/logout">Sign out</a>
  <% } %>
  ```

5.  Ajoutez une mise en page. Créez la vue `/views/layout.ejs` dans le répertoire racine.

  ```HTML

  <!DOCTYPE html>
  <html>
      <head>
          <title>Passport-OpenID Example</title>
      </head>
      <body>
          <% if (!user) { %>
              <p>
              <a href="/">Home</a> |
              <a href="/login">Sign in</a>
              </p>
          <% } else { %>
              <p>
              <a href="/">Home</a> |
              <a href="/account">Account</a> |
              <a href="/logout">Sign out</a>
              </p>
          <% } %>
          <%- body %>
      </body>
  </html>
  ```

6.  Exécutez `node app.js` pour générer et exécuter votre application. Ensuite, accédez à `http://localhost:3000`.

7.  Connectez-vous à un compte personnel Microsoft ou à un compte scolaire/professionnel. Notez que l’identité de l’utilisateur s’affiche dans la liste /account. 

Vous disposez maintenant d’une application web qui est sécurisée à l’aide de protocoles standard. Vous pouvez authentifier les utilisateurs dans votre application à l’aide de leurs comptes personnels, professionnels ou scolaires.

## <a name="next-steps"></a>Étapes suivantes
Pour référence, l’exemple terminé (sans vos valeurs de configuration) est fourni en tant que [fichier .zip](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs/archive/complete.zip). Vous pouvez également le cloner à partir de GitHub :

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git```

Vous pouvez maintenant aborder des rubriques plus avancées. Par exemple :

[Sécuriser une API web Node.js à l’aide du point de terminaison v2.0](active-directory-v2-devquickstarts-node-api.md)

Voici quelques ressources supplémentaires :

* [Guide du développeur Azure AD v2.0](active-directory-appmodel-v2-overview.md)
* [Balise « azure-active-directory » dans Stack Overflow](http://stackoverflow.com/questions/tagged/azure-active-directory)

### <a name="get-security-updates-for-our-products"></a>Obtenir les mises à jour de sécurité de nos produits
Nous vous encourageons à vous inscrire pour être averti quand des incidents de sécurité se produisent. Sur la page [Microsoft Technical Security Notifications](https://technet.microsoft.com/security/dd252948), abonnez-vous aux alertes d’avis de sécurité.

