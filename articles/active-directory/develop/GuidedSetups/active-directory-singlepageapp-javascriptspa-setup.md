---
title: "Configuration guidée d’Azure AD v2 JS SPA - Configuration | Microsoft Docs"
description: "Comment les applications JavaScript SPA peuvent appeler une API qui nécessite des jetons d’accès à partir d’un point de terminaison Azure Active Directory v2"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/01/2017
ms.author: andret
ms.openlocfilehash: 04485cae3c28a2e9f0c3bf9f32b1c3db482a8212
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/07/2017
---
## <a name="setting-up-your-web-server-or-project"></a>Configuration du serveur web ou du projet

> Vous préférez télécharger le projet de cet exemple ? 
> - [Télécharger le projet Visual Studio](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/VisualStudio.zip)
>
> ou
> - [Télécharger les fichiers de projet](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/core.zip) pour un serveur web local tel que Python
>
> Passez ensuite à l’étape [Configuration](#create-an-application-express) pour configurer l’exemple de code avant son exécution.

## <a name="prerequisites"></a>Composants requis
Pour exécuter cette installation guidée, vous avez besoin d’un serveur web local tel que [Python http-server](https://www.python.org/downloads/), [http-server](https://www.npmjs.com/package/http-server/) ou [.NET Core](https://www.microsoft.com/net/core), ou de l’intégration d’IIS Express à [Visual Studio 2017](https://www.visualstudio.com/downloads/). 

Les instructions de ce guide sont basées sur Python et Visual Studio 2017. Cependant, vous pouvez utiliser n’importe quel environnement de développement ou serveur web.

## <a name="create-your-project"></a>Créer votre projet 

> ### <a name="option-1-visual-studio"></a>Option 1 : Visual Studio 
> Si vous utilisez Visual Studio pour créer un projet, suivez les étapes ci-dessous pour créer une solution Visual Studio :
> 1.    Dans Visual Studio : `File` > `New` > `Project`
> 2.    Sous `Visual C#\Web`, sélectionnez `ASP.NET Web Application (.NET Framework)`
> 3.    Donnez un nom à votre application et cliquez sur *OK*.
> 4.    Sous `New ASP.NET Web Application`, sélectionnez `Empty`

<p/><!-- -->

> ### <a name="option-2-python-other-web-servers"></a>Option 2 : Python / autres serveurs web
> Vérifiez que vous avez installé [Python](https://www.python.org/downloads/), puis suivez les étapes ci-dessous :
> - Créez un dossier pour héberger votre application.


## <a name="create-your-single-page-applications-ui"></a>Créer l’interface utilisateur de votre application à page unique (SPA)
1.  Créez un fichier *index.html* pour votre application SPA JavaScript. Si vous utilisez Visual Studio, sélectionnez le projet (dossier racine du projet), cliquez avec le bouton droit, sélectionnez `Add` > `New Item` > `HTML page`, puis nommez-le index.html.
2.  Ajoutez le code suivant à votre page :
```html
<!DOCTYPE html>
<html>
<head>
    <!-- bootstrap reference used for styling the page -->
    <link href="//maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css" rel="stylesheet">
    <title>JavaScript SPA Guided Setup</title>
</head>
<body style="margin: 40px">
    <button id="callGraphButton" type="button" class="btn btn-primary" onclick="callGraphApi()">Call Microsoft Graph API</button>
    <div id="errorMessage" class="text-danger"></div>
    <div class="hidden">
        <h3>Graph API Call Response</h3>
        <pre class="well" id="graphResponse"></pre>
    </div>
    <div class="hidden">
        <h3>Access Token</h3>
        <pre class="well" id="accessToken"></pre>
    </div>
    <div class="hidden">
        <h3>ID Token Claims</h3>
        <pre class="well" id="userInfo"></pre>
    </div>
    <button id="signOutButton" type="button" class="btn btn-primary hidden" onclick="signOut()">Sign out</button>

    <!-- This app uses cdn to reference msal.js (recommended). 
         You can also download it from: https://github.com/AzureAD/microsoft-authentication-library-for-js -->
    <script src="https://secure.aadcdn.microsoftonline-p.com/lib/0.1.3/js/msal.min.js"></script>

    <!-- The 'bluebird' and 'fetch' references below are required if you need to run this application on Internet Explorer -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/fetch/2.0.3/fetch.min.js"></script>

    <script type="text/javascript" src="msalconfig.js"></script>
    <script type="text/javascript" src="app.js"></script>
</body>
</html>
````
