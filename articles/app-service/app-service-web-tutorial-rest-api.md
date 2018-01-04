---
title: Application API Node.js dans Azure App Service | Microsoft Docs
description: "Découvrez comment créer une API RESTful Node.js et la déployer vers une application API dans Azure App Service."
services: app-service\api
documentationcenter: node
author: bradygaster
manager: erikre
editor: 
ms.assetid: a820e400-06af-4852-8627-12b3db4a8e70
ms.service: app-service-api
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 06/13/2017
ms.author: rachelap
ms.custom: mvc, devcenter
ms.openlocfilehash: 2170ac7df3b894c8d19b432abdcfef5c7fd75ff4
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2017
---
# <a name="build-a-nodejs-restful-api-and-deploy-it-to-an-api-app-in-azure"></a>Créer une API RESTful Node.js et la déployer vers une application API dans Azure

Ce démarrage rapide montre comment créer une API REST, écrite avec Node.js [Express](http://expressjs.com/), en utilisant une définition [Swagger](http://swagger.io/) et en la déployant sur Azure. Vous créez l’application à l’aide d’outils en ligne de commande, configurez des ressources avec [l’interface de ligne de commande Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli), puis déployez l’application au moyen de Git.  Lorsque vous avez terminé, vous disposez d’un exemple d’API REST fonctionnelle qui s’exécute sur Azure.

## <a name="prerequisites"></a>configuration requise

* [Git](https://git-scm.com/)
* [Node.js et NPM](https://nodejs.org/)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, vous devez exécuter Azure CLI version 2.0 ou une version ultérieure pour poursuivre la procédure décrite dans cet article. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="prepare-your-environment"></a>Préparation de votre environnement

1. Dans une fenêtre de terminal, exécutez la commande ci-après pour cloner l’exemple sur votre ordinateur local.

    ```bash
    git clone https://github.com/Azure-Samples/app-service-api-node-contact-list
    ```

2. Passez au répertoire qui contient l’exemple de code.

    ```bash
    cd app-service-api-node-contact-list
    ```

3. Installez [Swaggerize](https://www.npmjs.com/package/swaggerize-express) sur votre ordinateur local. Swaggerize est un outil qui génère du code Node.js pour votre API REST à partir d’une définition Swagger.

    ```bash
    npm install -g yo
    npm install -g generator-swaggerize
    ```

## <a name="generate-nodejs-code"></a>Générer du code Node.js 

Cette section du didacticiel modélise un workflow de développement d’API dans lequel vous créez d’abord les métadonnées Swagger, puis les utilisez pour structurer (auto-générer) le code serveur pour l’API. 

Accédez au dossier *start*, puis exécutez `yo swaggerize`. Swaggerize crée un projet Node.js pour votre API à partir de la définition Swagger dans *api.json*.

```bash
cd start
yo swaggerize --apiPath api.json --framework express
```

Lorsque Swaggerize demande un nom de projet, utilisez *ContactList*.
   
   ```bash
   Swaggerize Generator
   Tell us a bit about your application
   ? What would you like to call this project: ContactList
   ? Your name: Francis Totten
   ? Your github user name: fabfrank
   ? Your email: frank@fabrikam.net
   ```
   
## <a name="customize-the-project-code"></a>Personnaliser le code du projet

1. Copiez le dossier *lib* dans le dossier *ContactList* créé par `yo swaggerize`, puis accédez au dossier *ContactList*.

    ```bash
    cp -r lib/ ContactList/
    cd ContactList
    ```

2. Installez les modules NPM `jsonpath` et `swaggerize-ui`. 

    ```bash
    npm install --save jsonpath swaggerize-ui
    ```

3. Remplacez le code figurant dans *handlers/contacts.js* par le code suivant : 
    ```javascript
    'use strict';

    var repository = require('../lib/contactRepository');

    module.exports = {
        get: function contacts_get(req, res) {
            res.json(repository.all())
        }
    };
    ```
    Ce code utilise les données JSON stockées dans *lib/contacts.json* traité par *lib/contactRepository.js*. Le nouveau code *contacts.js* renvoie tous les contacts du référentiel sous la forme d’une charge utile JSON. 

4. Remplacez le code du fichier **handlers/contacts/{id}.js** par le code suivant :

    ```javascript
    'use strict';

    var repository = require('../../lib/contactRepository');

    module.exports = {
        get: function contacts_get(req, res) {
            res.json(repository.get(req.params['id']));
        }    
    };
    ```

    Ce code vous permet d’utiliser une variable de chemin d’accès afin que seul le contact présentant un ID spécifique soit renvoyé.

5. Remplacez le code figurant dans **server.js** par le code suivant :

    ```javascript
    'use strict';

    var port = process.env.PORT || 8000; 

    var http = require('http');
    var express = require('express');
    var bodyParser = require('body-parser');
    var swaggerize = require('swaggerize-express');
    var swaggerUi = require('swaggerize-ui'); 
    var path = require('path');
    var fs = require("fs");
    
    fs.existsSync = fs.existsSync || require('path').existsSync;

    var app = express();

    var server = http.createServer(app);

    app.use(bodyParser.json());

    app.use(swaggerize({
        api: path.resolve('./config/swagger.json'),
        handlers: path.resolve('./handlers'),
        docspath: '/swagger' 
    }));

    // change four
    app.use('/docs', swaggerUi({
        docs: '/swagger'  
    }));

    server.listen(port, function () { 
    });
    ```   

    Ce code apporte quelques modifications minimes pour être en mesure de fonctionner avec Azure App Service et expose une interface web interactive pour votre API.

### <a name="test-the-api-locally"></a>Tester l’API localement

1. Démarrer l’application Node.js
    ```bash
    npm start
    ```
    
2. Accédez à http://localhost:8000/contacts pour visualiser le code JSON de l’intégralité de la liste de contacts.
   
   ```json
    {
        "id": 1,
        "name": "Barney Poland",
        "email": "barney@contoso.com"
    },
    {
        "id": 2,
        "name": "Lacy Barrera",
        "email": "lacy@contoso.com"
    },
    {
        "id": 3,
        "name": "Lora Riggs",
        "email": "lora@contoso.com"
    }
   ```

3. Accédez à http://localhost:8000/contacts/2 pour visualiser le contact présentant un `id` égal à 2.
   
    ```json
    { 
        "id": 2,
        "name": "Lacy Barrera",
        "email": "lacy@contoso.com"
    }
    ```

4. Testez l’API à l’aide de l’interface web Swagger à l’adresse http://localhost:8000/docs.
   
    ![Interface web Swagger](media/app-service-web-tutorial-rest-api/swagger-ui.png)

## <a id="createapiapp"></a> Créer une application API

Dans cette section, vous utilisez Azure CLI 2.0 afin de créer les ressources nécessaires pour l’hébergement de l’API sur Azure App Service. 

1.  Connectez-vous à votre abonnement Azure avec la commande [az login](/cli/azure/?view=azure-cli-latest#az_login) et suivez les instructions à l’écran.

    ```azurecli-interactive
    az login
    ```

2. Si vous avez plusieurs abonnements Azure, modifiez l’abonnement par défaut pour accéder à l’abonnement souhaité.

    ````azurecli-interactive
    az account set --subscription <name or id>
    ````

3. [!INCLUDE [Create resource group](../../includes/app-service-api-create-resource-group.md)] 

4. [!INCLUDE [Create app service plan](../../includes/app-service-api-create-app-service-plan.md)]

5. [!INCLUDE [Create API app](../../includes/app-service-api-create-api-app.md)] 


## <a name="deploy-the-api-with-git"></a>Déployer l’API avec Git

Déployez votre code dans l’application API en envoyant des validations de votre référentiel Git local vers Azure App Service.

1. [!INCLUDE [Configure your deployment credentials](../../includes/configure-deployment-user-no-h.md)] 

2. Initialisez un nouveau référentiel dans le répertoire *ContactList*. 

    ```bash
    git init .
    ```

3. Excluez de Git le répertoire *node_modules* créé par npm au cours d’une étape précédente du didacticiel. Créez un fichier `.gitignore` dans le répertoire actuel, puis ajoutez le texte ci-après sur une nouvelle ligne à un emplacement quelconque du fichier.

    ```
    node_modules/
    ```
    Vérifiez que le dossier `node_modules` est ignoré avec la commande `git status`.

4. Validez les modifications apportées au référentiel.
    ```bash
    git add .
    git commit -m "initial version"
    ```

5. [!INCLUDE [Push to Azure](../../includes/app-service-api-git-push-to-azure.md)]  
 
## <a name="test-the-api--in-azure"></a>Tester l’API dans Azure

1. Ouvrez un navigateur et accédez à http://app_name.azurewebsites.net/contacts. Le code JSON renvoyé est le même que lorsque vous avez exécuté la requête au niveau local lors d’une étape précédente du didacticiel.

   ```json
   {
       "id": 1,
       "name": "Barney Poland",
       "email": "barney@contoso.com"
   },
   {
       "id": 2,
       "name": "Lacy Barrera",
       "email": "lacy@contoso.com"
   },
   {
       "id": 3,
       "name": "Lora Riggs",
       "email": "lora@contoso.com"
   }
   ```

2. Dans un navigateur, accédez au point de terminaison `http://app_name.azurewebsites.net/docs` pour tester l’interface utilisateur Swagger s’exécutant sur Azure.

    ![Interface utilisateur Swagger](media/app-service-web-tutorial-rest-api/swagger-azure-ui.png)

    Vous pouvez désormais déployer des mises à jour de l’exemple d’API vers Azure en envoyant simplement des validations au référentiel Git Azure.

## <a name="clean-up"></a>Nettoyer

Pour nettoyer les ressources créées dans le cadre de ce guide de démarrage rapide, exécutez la commande Azure CLI suivante :

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-step"></a>Étape suivante 
> [!div class="nextstepaction"]
> [Mapper un nom DNS personnalisé existant à des applications web Azure](app-service-web-tutorial-custom-domain.md)

