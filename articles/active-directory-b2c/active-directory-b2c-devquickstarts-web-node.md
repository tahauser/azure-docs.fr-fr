---
title: Ajouter la connexion à une application web Node.js - Azure Active Directory B2C
description: Comment créer une application web Node.js qui connecte les utilisateurs à Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 03/10/2017
ms.author: davidmu
ms.openlocfilehash: b10b2338bfbcd449075e612ae00cc23802dce3a9
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-ad-b2c-add-sign-in-to-a-nodejs-web-app"></a>Azure AD B2C : ajouter une connexion à une application web Node.js

**Passport** est un intergiciel d’authentification pour Node.js. Flexible et modulaire, Passport peut être installé discrètement dans toute application web basée sur Express ou Restify. Une gamme complète de stratégies prend en charge l’authentification à l’aide d’un nom d’utilisateur et d’un mot de passe, de Facebook, de Twitter,etc.

Pour Azure Active Directory (Azure AD), vous pouvez installer ce module, puis y ajouter le plug-in `passport-azure-ad` Active Directory Azure.

Vous devez :

1. Inscrivez une application en utilisant Azure AD.
2. Configurez votre application pour utiliser le plug-in `passport-azure-ad`.
3. utiliser Passport pour émettre des demandes de connexion et de déconnexion dans Azure AD ;
4. Imprimez les données utilisateur.

Le code associé à ce didacticiel [est stocké sur GitHub](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS). Pour suivre la procédure, vous pouvez [télécharger la structure de l’application au format .zip](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS/archive/master.zip). Vous pouvez également cloner la structure :

```git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS.git```

L’application terminée est également fournie à la fin de ce didacticiel.

## <a name="get-an-azure-ad-b2c-directory"></a>Obtention d'un répertoire Azure AD B2C

Avant de pouvoir utiliser Azure AD B2C, vous devez créer un répertoire ou un client.  Un répertoire est un conteneur destiné à recevoir tous vos utilisateurs, applications, groupes, etc. Si vous n’en possédez pas déjà un, [créez un répertoire B2C](active-directory-b2c-get-started.md) avant d’aller plus loin dans ce guide.

## <a name="create-an-application"></a>Création d'une application

Vous devez maintenant créer dans votre répertoire B2C une application fournissant à Azure AD les informations nécessaires pour communiquer avec votre application en toute sécurité. Dans ce cas, l’application cliente et l’API web seront toutes les deux représentées par un seul **ID d’application**, car elles constituent une application logique. Pour créer une application, suivez [ces instructions](active-directory-b2c-app-registration.md). Veillez à effectuer les opérations suivantes :

- Inclure une **application web**/**API web** dans l’application.
- Entrez `http://localhost:3000/auth/openid/return` comme **URL de réponse**. Il s’agit de l’URL par défaut pour cet exemple de code.
- Créez un **secret d’application** pour votre application et copiez-le. Vous en aurez besoin ultérieurement. Notez que cette valeur doit être [placée dans une séquence d’échappement XML](https://www.w3.org/TR/2006/REC-xml11-20060816/#dt-escape) avant son utilisation.
- Copiez l’ **ID d’application** affecté à votre application. Vous allez également en avoir besoin par la suite.

## <a name="create-your-policies"></a>Création de vos stratégies

Dans Azure AD B2C, chaque expérience utilisateur est définie par une [stratégie](active-directory-b2c-reference-policies.md). Cette application contient trois expériences liées à l’identité : l’inscription, la connexion et la connexion avec Facebook. Vous devez créer cette stratégie pour chaque type, comme décrit dans [l’article de référence sur les stratégies](active-directory-b2c-reference-policies.md#create-a-sign-up-policy). Lors de la création de vos 3 stratégies, assurez-vous de :

- Choisir le **Nom d’affichage** et d’autres attributs d’inscription dans votre stratégie d’inscription.
- Choisissez le **nom d’affichage** et **l’ID objet** comme revendications d’application pour chaque stratégie. Vous pouvez aussi choisir d'autres revendications.
- Copiez le **nom** de chaque stratégie après sa création. Il doit porter le préfixe `b2c_1_`.  Vous aurez besoin des noms de ces stratégies ultérieurement.

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Une fois vos trois stratégies créées, vous pouvez générer votre application.

Remarque : cet article n'explique pas comment utiliser les stratégies que vous venez de créer. Pour en savoir plus sur le fonctionnement des stratégies dans Azure AD B2C, commencez par consulter le [didacticiel de prise en main des applications web .NET](active-directory-b2c-devquickstarts-web-dotnet.md).

## <a name="add-prerequisites-to-your-directory"></a>Ajout d’éléments requis au répertoire

Depuis la ligne de commande, accédez aux répertoires de votre dossier racine si ce n’est pas déjà fait : Exécutez les commandes suivantes :

- `npm install express`
- `npm install ejs`
- `npm install ejs-locals`
- `npm install restify`
- `npm install mongoose`
- `npm install bunyan`
- `npm install assert-plus`
- `npm install passport`
- `npm install webfinger`
- `npm install body-parser`
- `npm install express-session`
- `npm install cookie-parser`

En outre, nous avons utilisé `passport-azure-ad` pour notre version préliminaire dans la structure du démarrage rapide.

- `npm install passport-azure-ad`

Cela va installer les bibliothèques dont dépend `passport-azure-ad`.

## <a name="set-up-your-app-to-use-the-passport-nodejs-strategy"></a>Configuration de l’application pour utiliser la stratégie Passport-Node.js
Configurez l’intergiciel Express pour utiliser le protocole d’authentification OpenID Connect. Passport est utilisé notamment pour émettre des demandes de connexion et de déconnexion, gérer les sessions utilisateur et obtenir des informations concernant l’utilisateur.

Ouvrez le fichier `config.js` à la racine du projet, puis entrez les valeurs de configuration de votre application dans la section `exports.creds`.
- `clientID` : **ID d’application** affecté à votre application dans le portail d’inscription.
- `returnURL` : **URI de redirection** saisi dans le portail.
- `tenantName` : nom de client de votre application, par exemple **contoso.onmicrosoft.com**.

[!INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

Ouvrez le fichier `app.js` à la racine du projet. Ajoutez l’appel suivant pour appeler la stratégie `OIDCStrategy` qui accompagne `passport-azure-ad`.


```javascript
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

// Add some logging
var log = bunyan.createLogger({
    name: 'Microsoft OIDC Example Web Application'
});
```

Utilisez la stratégie référencée pour gérer les demandes de connexion.

```javascript
// Use the OIDCStrategy in Passport (Section 2).
//
//   Strategies in Passport require a "validate" function that accepts
//   credentials (in this case, an OpenID identifier), and invokes a callback
//   by using a user object.
passport.use(new OIDCStrategy({
    callbackURL: config.creds.returnURL,
    realm: config.creds.realm,
    clientID: config.creds.clientID,
    oidcIssuer: config.creds.issuer,
    identityMetadata: config.creds.identityMetadata,
    skipUserProfile: config.creds.skipUserProfile,
    responseType: config.creds.responseType,
    responseMode: config.creds.responseMode,
    tenantName: config.creds.tenantName
  },
  function(iss, sub, profile, accessToken, refreshToken, done) {
    log.info('Example: Email address we received was: ', profile.email);
    // asynchronous verification, for effect...
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
Passport utilise un modèle similaire pour l’ensemble de ses stratégies (y compris Twitter et Facebook). Tous les enregistreurs de stratégie adhèrent à ce modèle. Lorsque vous examinez la stratégie, vous pouvez voir que vous lui transmettez un paramètre `function()` qui dispose d’un jeton et un paramètre `done`. La stratégie vous revient après avoir effectué toutes les tâches. Vous devez ensuite stocker l’utilisateur et enregistrer le jeton pour ne pas avoir à le demander de nouveau.

> [!IMPORTANT]
Le code précédent prend tous les utilisateurs auxquels le serveur s’authentifie. C’est ce que l’on appelle l’enregistrement automatique. Lorsque vous utilisez des serveurs de production, vous ne souhaitez pas laisser entrer des utilisateurs, sauf s’ils ont suivi la procédure d’enregistrement que vous avez mise en place. C’est le modèle souvent utilisé dans les applications grand public. Elles vous permettent de vous inscrire à l’aide de Facebook, mais elles vous demandent ensuite de fournir des informations supplémentaires. Si notre application n’était pas un exemple, nous pourrions extraire l’adresse de messagerie à partir de l’objet de jeton retourné, puis demander à l’utilisateur d’entrer des informations supplémentaires. Étant donné qu’il s’agit d’un serveur de test, nous ajoutons simplement les utilisateurs à la base de données en mémoire.

Ajoutez les méthodes qui vous permettent d’effectuer le suivi des utilisateurs qui se sont connectés, comme requis par Passport. Cela inclut la sérialisation et la désérialisation des informations des utilisateurs :

```javascript

// Passport session setup. (Section 2)

//   To support persistent sign-in sessions, Passport needs to be able to
//   serialize users into and deserialize users out of sessions. Typically,
//   this is as simple as storing the user ID when Passport serializes a user
//   and finding the user by ID when Passport deserializes that user.
passport.serializeUser(function(user, done) {
  done(null, user.email);
});

passport.deserializeUser(function(id, done) {
  findByEmail(id, function (err, user) {
    done(err, user);
  });
});

// Array to hold users who have signed in
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

Ensuite, ajoutez le code pour charger le moteur Express. Dans ce qui suit, vous pouvez voir que nous utilisons la valeur par défaut `/views` et le modèle `/routes` fourni par Express.

```javascript

// configure Express (Section 2)

var app = express();


app.configure(function() {
  app.set('views', __dirname + '/views');
  app.set('view engine', 'ejs');
  app.use(express.logger());
  app.use(express.methodOverride());
  app.use(cookieParser());
  app.use(expressSession({ secret: 'keyboard cat', resave: true, saveUninitialized: false }));
  app.use(bodyParser.urlencoded({ extended : true }));
  // Initialize Passport!  Also use passport.session() middleware to support
  // persistent sign-in sessions (recommended).
  app.use(passport.initialize());
  app.use(passport.session());
  app.use(app.router);
  app.use(express.static(__dirname + '/../../public'));
});

```

Ajoutez maintenant les itinéraires `POST` qui transmettent les demandes d’ouverture de session au moteur `passport-azure-ad` :

```javascript

// Our Auth routes (Section 3)

// GET /auth/openid
//   Use passport.authenticate() as route middleware to authenticate the
//   request. The first step in OpenID authentication involves redirecting
//   the user to an OpenID provider. After the user is authenticated,
//   the OpenID provider redirects the user back to this application at
//   /auth/openid/return

app.get('/auth/openid',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    log.info('Authentication was called in the Sample');
    res.redirect('/');
  });

// GET /auth/openid/return
//   Use passport.authenticate() as route middleware to authenticate the
//   request. If authentication fails, the user will be redirected back to the
//   sign-in page. Otherwise, the primary route function will be called.
//   In this example, it redirects the user to the home page.
app.get('/auth/openid/return',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {

    res.redirect('/');
  });

// POST /auth/openid/return
//   Use passport.authenticate() as route middleware to authenticate the
//   request. If authentication fails, the user will be redirected back to the
//   sign-in page. Otherwise, the primary route function will be called.
//   In this example, it will redirect the user to the home page.

app.post('/auth/openid/return',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {

    res.redirect('/');
  });
```

## <a name="use-passport-to-issue-sign-in-and-sign-out-requests-to-azure-ad"></a>Utilisation de Passport pour émettre des demandes de connexion et de déconnexion dans Azure AD

Votre application est maintenant correctement configurée pour communiquer avec le point de terminaison v2.0 en utilisant le protocole d’authentification OpenID Connect. `passport-azure-ad` a pris en charge tous les détails de la création de messages d’authentification, de la validation des jetons d’Azure AD et de la gestion des sessions utilisateur. Il ne reste plus qu’à fournir aux utilisateurs un moyen de se connecter, de se déconnecter et de collecter des informations supplémentaires une fois connectés.

Tout d’abord, ajoutez au fichier `app.js` les méthodes par défaut de connexion, de compte et de déconnexion :

```javascript

//Routes (Section 4)

app.get('/', function(req, res){
  res.render('index', { user: req.user });
});

app.get('/account', ensureAuthenticated, function(req, res){
  res.render('account', { user: req.user });
});

app.get('/login',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    log.info('Login was called in the Sample');
    res.redirect('/');
});

app.get('/logout', function(req, res){
  req.logout();
  res.redirect('/');
});

```

Pour consulter le détail de ces méthodes :
- L’itinéraire `/` redirige vers la vue `index.ejs` en transférant l’utilisateur dans la demande (le cas échéant).
- L’itinéraire `/account` vérifie tout d’abord que vous êtes authentifié (l’implémentation de ce point figure ci-dessous). Il transfère ensuite l’utilisateur dans la demande afin que vous puissiez obtenir plus d’informations sur lui.
- L’itinéraire `/login` appelle l’authentificateur `azuread-openidconnect` depuis `passport-azure-ad`. En cas d’échec, l’itinéraire redirige l’utilisateur vers `/login`.
- `/logout` appelle simplement `logout.ejs` (et son itinéraire). Cela efface les cookies, puis renvoie l’utilisateur au `index.ejs`.


Pour la dernière partie de `app.js`, ajoutez la méthode `EnsureAuthenticated` utilisée dans l’itinéraire `/account`.

```javascript

// Simple route middleware to ensure that the user is authenticated. (Section 4)

//   Use this route middleware on any resource that needs to be protected. If
//   the request is authenticated (typically via a persistent sign-in session),
//   then the request will proceed. Otherwise, the user will be redirected to the
//   sign-in page.
function ensureAuthenticated(req, res, next) {
  if (req.isAuthenticated()) { return next(); }
  res.redirect('/login')
}

```

Enfin, créez le serveur proprement dit dans `app.js`.

```javascript

app.listen(3000);

```


## <a name="create-the-views-and-routes-in-express-to-call-your-policies"></a>Création des vues et itinéraires dans Express pour appeler les stratégies

La création du fichier `app.js` est maintenant terminée. Vous devez simplement ajouter les itinéraires et les vues qui vous permettent d’appeler les stratégies de connexion et d’inscription. Cela permet également de gérer les itinéraires `/logout` et `/login` que vous avez créés.

Créez l’itinéraire `/routes/index.js` sous le répertoire racine.

```javascript

/*
 * GET home page.
 */

exports.index = function(req, res){
  res.render('index', { title: 'Express' });
};
```

Créez l’itinéraire `/routes/user.js` sous le répertoire racine.

```javascript

/*
 * GET users listing.
 */

exports.list = function(req, res){
  res.send("respond with a resource");
};
```

Ces itinéraires simples transmettent les demandes à vos vues. Ils intègrent l’utilisateur, s’il en existe un.

Créez la vue `/views/index.ejs` sous le répertoire racine. Il s’agit d’une page simple qui appelle des stratégies de connexion et de déconnexion. Vous pouvez également l’utiliser pour récupérer les informations de compte. Notez que vous pouvez utiliser la condition `if (!user)` dès lors que l’utilisateur est transmis dans la demande pour fournir la preuve qu’il est bien connecté.

```javascript
<% if (!user) { %>
    <h2>Welcome! Please sign in.</h2>
    <a href="/login/?p=your facebook policy">Sign in with Facebook</a>
    <a href="/login/?p=your email sign-in policy">Sign in with email</a>
    <a href="/login/?p=your email sign-up policy">Sign up with email</a>
<% } else { %>
    <h2>Hello, <%= user.displayName %>.</h2>
    <a href="/account">Account info</a></br>
    <a href="/logout">Log out</a>
<% } %>
```

Créez la vue `/views/account.ejs` sous le répertoire racine afin d’afficher les informations supplémentaires que `passport-azure-ad` a placées dans la demande de l’utilisateur.

```javascript
<% if (!user) { %>
    <h2>Welcome! Please sign in.</h2>
    <a href="/login">Sign in</a>
<% } else { %>
<p>displayName: <%= user.displayName %></p>
<p>givenName: <%= user.name.givenName %></p>
<p>familyName: <%= user.name.familyName %></p>
<p>UPN: <%= user._json.upn %></p>
<p>Profile ID: <%= user.id %></p>
<p>Full Claims</p>
<%- JSON.stringify(user) %>
<p></p>
<a href="/logout">Log Out</a>
<% } %>
```

Vous pouvez maintenant créer et exécuter votre application

Exécutez `node app.js` et accédez à `http://localhost:3000`


Inscrivez-vous ou connectez-vous à l’application avec une adresse de messagerie ou via Facebook. Déconnectez-vous, puis reconnectez-vous comme si vous étiez un autre utilisateur.

## <a name="next-steps"></a>Étapes suivantes

Pour référence, l’exemple terminé (sans vos valeurs de configuration) [est fourni en tant que fichier .zip](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS/archive/complete.zip). Vous pouvez également le cloner à partir de GitHub :

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-nodejs.git```

Vous pouvez maintenant aborder des rubriques plus avancées. Vous pouvez essayer :

[Sécurisation d’une API web avec le modèle B2C dans Node.js](active-directory-b2c-devquickstarts-api-node.md)

<!--

For additional resources, check out:
You can now move on to more advanced B2C topics. You might try:

[Call a Node.js web API from a Node.js web app]()

[Customizing the your B2C App's UX >>]()

-->
