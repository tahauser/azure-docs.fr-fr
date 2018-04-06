---
title: Générer une application web Node.js pour Azure Cosmos DB | Microsoft Docs
description: Ce didacticiel Node.js explique comment utiliser Microsoft Azure Cosmos DB pour stocker des données et y accéder à partir d’une application web Express Node.js hébergée sur les sites web Azure.
keywords: Développement d’applications, didacticiel de base de données, apprendre node.js, didacticiel node.js
services: cosmos-db
documentationcenter: nodejs
author: mimig1
manager: jhubbard
editor: cgronlun
ms.assetid: 9da9e63b-e76a-434e-96dd-195ce2699ef3
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 03/23/2018
ms.author: mimig
ms.openlocfilehash: ad6e335c562e52d7e2336dd1f29e5c159fe46589
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="_Toc395783175"></a>Création d’une application web Node.js avec Azure Cosmos DB
> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Node.JS](sql-api-nodejs-application.md)
> * [Java](sql-api-java-application.md)
> * [Python](sql-api-python-application.md)
> 
> 

Ce didacticiel Node.js vous montre comment utiliser le service Azure Cosmos DB et l’API SQL pour stocker des données et y accéder à partir d’une application Express Node.js hébergée sur les sites web Azure. Vous allez créer une simple application web de gestion des tâches, une application ToDo, qui permet de créer, de récupérer et de terminer des tâches. Ces dernières sont stockées en tant que documents JSON dans AzureCosmos DB. Ce didacticiel vous guide à travers la création et le déploiement de l’application et explique ce qui se passe dans chaque extrait de code.

![Capture d’écran de l’application My Todo List créée dans ce didacticiel Node.js](./media/sql-api-nodejs-application/cosmos-db-node-js-mytodo.png)

Vous n'avez pas le temps de terminer le didacticiel et vous souhaitez simplement obtenir la solution complète ? Vous pouvez obtenir facilement l’exemple de solution complet sur [GitHub][GitHub]. Lire simplement le fichier [Lisez-moi](https://github.com/Azure-Samples/documentdb-node-todo-app/blob/master/README.md) pour obtenir des instructions sur l’exécution de l’application.

## <a name="_Toc395783176"></a>Configuration requise
> [!TIP]
> Ce didacticiel Node.js part du principe que vous avez déjà utilisé Node.js et Sites Web Azure.
> 
> 

Avant de suivre les instructions de cet article, vérifiez que les éléments suivants sont installés :

* Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer. 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Node.js][Node.js] version v0.10.29 ou supérieure. Nous vous recommandons Node.js 6.10 ou version ultérieure.
* [Générateur Express](http://www.expressjs.com/starter/generator.html) (installation possible via `npm install express-generator -g`)
* [Git][Git].

## <a name="_Toc395637761"></a>Étape 1 : création d’un compte de base de données Azure Cosmos DB
Commençons par créer un compte Azure Cosmos DB. Si vous possédez déjà un compte ou si vous utilisez l’émulateur Azure Cosmos DB pour ce didacticiel, vous pouvez passer à [l’étape 2 : création d’une application Node.js](#_Toc395783178).

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [cosmos-db-keys](../../includes/cosmos-db-keys.md)]

## <a name="_Toc395783178"></a>Étape 2 : création d’une application Node.js
Voyons maintenant comment créer un projet Node.js « Hello World » de base à l’aide de l’infrastructure [Express](http://expressjs.com/) .

1. Ouvrez votre terminal préféré, par exemple l’invite de commande Node.js.
2. Accédez au répertoire dans lequel vous souhaitez stocker la nouvelle application.
3. Utilisez le générateur Express pour générer une nouvelle application appelée **todo**.
   
        express todo
4. Ouvrez votre nouveau répertoire **todo** et installez les dépendances.
   
        cd todo
        npm install
5. Exécutez votre nouvelle application.
   
        npm start
6. Vous pouvez afficher votre nouvelle application en accédant à l’adresse [http://localhost:3000](http://localhost:3000) dans votre navigateur.
   
    ![Découverte de Node.js - Capture d’écran de l’application Hello World dans une fenêtre de navigateur](./media/sql-api-nodejs-application/cosmos-db-node-js-express.png)

    Ensuite, pour arrêter l’application, appuyez sur Ctrl + C dans la fenêtre de terminal, puis, sur les machines Windows uniquement, entrez **y** pour arrêter le traitement par lots.

## <a name="_Toc395783179"></a>Étape 3 : installation de modules supplémentaires
Le fichier **package.json** est l'un des fichiers créés à la racine du projet. Il contient une liste de modules supplémentaires qui sont nécessaires pour les applications Node.js. Ensuite, lorsque vous déployez cette application sur des sites web Azure, ce fichier permet de déterminer quels modules doivent être installés sur Azure pour la prise en charge de votre application. Nous avons besoin d'installer deux autres packages pour ce didacticiel.

1. De retour dans le terminal, installez le module **async** via npm.
   
        npm install async --save
2. Installez le module **documentdb** via npm. C’est dans ce module que se produit toute la magie d’Azure Cosmos DB.
   
        npm install documentdb --save

## <a name="_Toc395783180"></a>Étape 4 : utilisation du service Azure Cosmos DB dans une application Node
Ceci concerne l’ensemble de l’installation et de la configuration initiales. Venons-en à présent à la raison de notre présence ici, à savoir écrire du code avec Azure Cosmos DB.

### <a name="create-the-model"></a>Création du modèle
1. Dans le répertoire du projet, créez un répertoire nommé **models** dans le même répertoire que le fichier package.json.
2. Dans le répertoire **models**, créez un fichier appelé **task-model.js**. Ce fichier contiendra le modèle des tâches créées par votre application.
3. Dans le même répertoire **models**, créez un autre fichier nommé **cosmosdb-manager.js**. Ce fichier contient du code utile et réutilisable que nous utiliserons dans notre application. 
4. Copiez le code suivant dans **cosmosdb-manager.js**
    ```nodejs
    let DocumentDBClient = require('documentdb').DocumentClient;

    module.exports = {
    getOrCreateDatabase: (client, databaseId, callback) => {
        let querySpec = {
        query: 'SELECT * FROM root r WHERE r.id = @id',
        parameters: [{ name: '@id', value: databaseId }]
        };

        client.queryDatabases(querySpec).toArray((err, results) => {
        if (err) {
            callback(err);
        } else {
            if (results.length === 0) {
            let databaseSpec = { id: databaseId };
            client.createDatabase(databaseSpec, (err, created) => {
                callback(null, created);
            });
            } else {
            callback(null, results[0]);
            }
        }
        });
    },

    getOrCreateCollection: (client, databaseLink, collectionId, callback) => {
        let querySpec = {
        query: 'SELECT * FROM root r WHERE r.id=@id',
        parameters: [{ name: '@id', value: collectionId }]
        };

        client.queryCollections(databaseLink, querySpec).toArray((err, results) => {
        if (err) {
            callback(err);
        } else {
            if (results.length === 0) {
            let collectionSpec = { id: collectionId };
            client.createCollection(databaseLink, collectionSpec, (err, created) => {
                callback(null, created);
            });
            } else {
            callback(null, results[0]);
            }
        }
        });
    }
    };
    ```
5. Enregistrez et fermez le fichier **cosmosdb-manager.js**.
6. Au début du fichier **task-model.js**, ajoutez le code suivant pour référencer **DocumentDBClient** et le fichier **cosmosdb-manager.js** créé précédemment : 

    ```nodejs
    let DocumentDBClient = require('documentdb').DocumentClient;
    let docdbUtils = require('./cosmosdb-manager.js');
    ```
7. Ensuite, vous allez ajouter du code pour définir et exporter l'objet Task. Il est responsable de l'initialisation de notre objet Task et de la configuration de la base de données et de la collection de documents que nous allons utiliser.  

    ```nodejs
    function TaskModel(documentDBClient, databaseId, collectionId) {
      this.client = documentDBClient;
      this.databaseId = databaseId;
      this.collectionId = collectionId;
   
      this.database = null;
      this.collection = null;
    }
   
    module.exports = TaskModel;
    ```
8. Ensuite, ajoutez le code suivant pour définir des méthodes supplémentaires sur l’objet Task permettant d’interagir avec les données stockées dans Azure Cosmos DB.

    ```nodejs
    let DocumentDBClient = require('documentdb').DocumentClient;
    let docdbUtils = require('./cosmosdb-manager');

    function TaskModel(documentDBClient, databaseId, collectionId) {
    this.client = documentDBClient;
    this.databaseId = databaseId;
    this.collectionId = collectionId;

    this.database = null;
    this.collection = null;
    }

    TaskModel.prototype = {
    init: function(callback) {
        let self = this;

        docdbUtils.getOrCreateDatabase(self.client, self.databaseId, function(err, db) {
        if (err) {
            callback(err);
        } else {
            self.database = db;
            docdbUtils.getOrCreateCollection(self.client, self.database._self, self.collectionId, function(err, coll) {
            if (err) {
                callback(err);
            } else {
                self.collection = coll;
            }
            });
        }
        });
    },

    find: function(querySpec, callback) {
        let self = this;

        self.client.queryDocuments(self.collection._self, querySpec).toArray(function(err, results) {
        if (err) {
            callback(err);
        } else {
            callback(null, results);
        }
        });
    },

    addItem: function(item, callback) {
        let self = this;

        item.date = Date.now();
        item.completed = false;

        self.client.createDocument(self.collection._self, item, function(err, doc) {
        if (err) {
            callback(err);
        } else {
            callback(null, doc);
        }
        });
    },

    updateItem: function(itemId, callback) {
        let self = this;

        self.getItem(itemId, function(err, doc) {
        if (err) {
            callback(err);
        } else {
            doc.completed = true;

            self.client.replaceDocument(doc._self, doc, function(err, replaced) {
            if (err) {
                callback(err);
            } else {
                callback(null, replaced);
            }
            });
        }
        });
    },

    getItem: function(itemId, callback) {
        let self = this;
        let querySpec = {
        query: 'SELECT * FROM root r WHERE r.id = @id',
        parameters: [{ name: '@id', value: itemId }]
        };

        self.client.queryDocuments(self.collection._self, querySpec).toArray(function(err, results) {
        if (err) {
            callback(err);
        } else {
            callback(null, results[0]);
        }
        });
    }
    };

    module.exports = TaskModel;
    ```
9. Enregistrez et fermez le fichier **task-model.js**. 

### <a name="create-the-controller"></a>Création du contrôleur
1. Dans le répertoire **routes** de votre projet, créez un fichier nommé **tasklist.js**. 
2. Ajoutez le code suivant dans **tasklist.js**. Ce code charge DocumentDBClient et les modules asynchrones, qui sont utilisés par **tasklist.js**. Il permet également de définir la fonction **TaskList**, à qui est transmise une instance de l’objet **Task** défini précédemment :
   
    ```nodejs
    let DocumentDBClient = require('documentdb').DocumentClient;
    let async = require('async');

    function TaskList(taskModel) {
    this.taskModel = taskModel;
    }

    module.exports = TaskList;
    ```
3. Continuez à modifier le fichier **tasklist.js** en ajoutant les méthodes utilisées pour **afficher les tâches (showTasks)** et **marquer les tâches comme terminées (completeTasks)** :
   
   ```nodejs
    TaskList.prototype = {
    showTasks: function(req, res) {
        let self = this;

        let querySpec = {
        query: 'SELECT * FROM root r WHERE r.completed=@completed',
        parameters: [
            {
            name: '@completed',
            value: false
            }
        ]
        };

        self.taskModel.find(querySpec, function(err, items) {
        if (err) {
            throw err;
        }

        res.render('index', {
            title: 'My ToDo List ',
            tasks: items
        });
        });
    },

    addTask: function(req, res) {
        let self = this;
        let item = req.body;

        self.taskModel.addItem(item, function(err) {
        if (err) {
            throw err;
        }

        res.redirect('/');
        });
    },

    completeTask: function(req, res) {
        let self = this;
        let completedTasks = Object.keys(req.body);

        async.forEach(
        completedTasks,
        function taskIterator(completedTask, callback) {
            self.taskModel.updateItem(completedTask, function(err) {
            if (err) {
                callback(err);
            } else {
                callback(null);
            }
            });
        },
        function goHome(err) {
            if (err) {
            throw err;
            } else {
            res.redirect('/');
            }
        }
        );
    }
    };
    ```        
4. Enregistrez et fermez le fichier **tasklist.js** .

### <a name="add-configjs"></a>Ajout de config.js
1. Dans le répertoire de projet, créez un fichier nommé **config.js**.
2. Ajoutez le code suivant à **config.js**. Définit les paramètres de configuration et les valeurs nécessaires à notre application.
   
    ```nodejs
    let config = {}
   
    config.host = process.env.HOST || "[the URI value from the Azure Cosmos DB Keys page on http://portal.azure.com]";
    config.authKey = process.env.AUTH_KEY || "[the PRIMARY KEY value from the Azure Cosmos DB Keys page on http://portal.azure.com]";
    config.databaseId = "ToDoList";
    config.collectionId = "Items";
   
    module.exports = config;
    ```
3. Dans le fichier **config.js**, mettez à jour les valeurs des paramètres HOST et AUTH_KEY en indiquant les valeurs trouvées dans la page Clés de votre compte Azure Cosmos DB, dans le [portail Microsoft Azure](https://portal.azure.com).
4. Enregistrez et fermez le fichier **config.js** .

### <a name="modify-appjs"></a>Modification de app.js
1. Dans le répertoire du projet, ouvrez le fichier **app.js** . Ce fichier a été créé précédemment lors de la création de l'application web Express.
2. Ajoutez le code suivant au début du fichier **app.js** :
   
    ```nodejs
    var DocumentDBClient = require('documentdb').DocumentClient;
    var config = require('./config');
    var TaskList = require('./routes/tasklist');
    var TaskModel = require('./models/taskModel');
    ```
3. Ce code définit le fichier de configuration à utiliser et procède à la lecture des valeurs de ce fichier dans des variables que nous utiliserons prochainement.
4. Remplacez les deux lignes suivantes dans le fichier **app.js** :
   
    ```nodejs
    app.use('/', index);
    app.use('/users', users); 
    ```
   
    par l'extrait de code suivant :
   
    ```nodejs
    let docDbClient = new DocumentDBClient(config.host, {
        masterKey: config.authKey
    });
    let taskModel = new TaskModel(docDbClient, config.databaseId, config.collectionId);
    let taskList = new TaskList(taskModel);
    taskModel.init();
   
    app.get('/', taskList.showTasks.bind(taskList));
    app.post('/addtask', taskList.addTask.bind(taskList));
    app.post('/completetask', taskList.completeTask.bind(taskList));
    app.set('view engine', 'jade');
    ```
5. Ces lignes définissent une nouvelle instance de notre objet **TaskModel**, avec une nouvelle connexion à Azure Cosmos DB (à l’aide des valeurs lues dans **config.js**), initialisent l’objet Task et relient les actions de formulaire à des méthodes dans notre contrôleur **TaskList**. 
6. Enfin, enregistrez et fermez le fichier **app.js**. Nous avons presque terminé.

## <a name="_Toc395783181"></a>Étape 5 : création d'une interface utilisateur
Intéressons-nous à présent à la création de l'interface utilisateur pour permettre à un utilisateur d'interagir réellement avec notre application. L'application Express que nous avons créée utilise **Jade** comme moteur de vue. Pour plus d’informations sur Jade, voir [http://jade-lang.com/](http://jade-lang.com/).

1. Le fichier **layout.jade** du répertoire **views** sert de modèle global aux autres fichiers **.jade**. Dans cette étape, vous allez le modifier pour utiliser [Twitter Bootstrap](https://github.com/twbs/bootstrap), qui est un kit de ressources qui facilite la conception d'un site web bien présenté. 
2. Ouvrez le fichier **layout.jade** trouvé dans le dossier **views** et remplacez le contenu par le code suivant :

    ```
    doctype html
    html
      head
        title= title
        link(rel='stylesheet', href='//ajax.aspnetcdn.com/ajax/bootstrap/3.3.2/css/bootstrap.min.css')
        link(rel='stylesheet', href='/stylesheets/style.css')
      body
        nav.navbar.navbar-inverse.navbar-fixed-top
          div.navbar-header
            a.navbar-brand(href='#') My Tasks
        block content
        script(src='//ajax.aspnetcdn.com/ajax/jQuery/jquery-1.11.2.min.js')
        script(src='//ajax.aspnetcdn.com/ajax/bootstrap/3.3.2/bootstrap.min.js')
    ```

    Ce code demande au moteur **Jade** de générer un rendu HTML pour notre application et crée un **bloc** intitulé **content** dans lequel nous pouvons fournir la mise en page de nos pages de contenu.

    Enregistrez et fermez ce fichier **layout.jade** .

3. Ouvrez maintenant le fichier **index.jade** , la vue qui sera utilisée par l'application, et remplacez le contenu du fichier par le code suivant :
   
        extends layout
        block content
           h1 #{title}
           br
        
           form(action="/completetask", method="post")
             table.table.table-striped.table-bordered
               tr
                 td Name
                 td Category
                 td Date
                 td Complete
               if (typeof tasks === "undefined")
                 tr
                   td
               else
                 each task in tasks
                   tr
                     td #{task.name}
                     td #{task.category}
                     - var date  = new Date(task.date);
                     - var day   = date.getDate();
                     - var month = date.getMonth() + 1;
                     - var year  = date.getFullYear();
                     td #{month + "/" + day + "/" + year}
                     td
                       input(type="checkbox", name="#{task.id}", value="#{!task.completed}", checked=task.completed)
             button.btn.btn-primary(type="submit") Update tasks
           hr
           form.well(action="/addtask", method="post")
             .form-group
               label(for="name") Item Name:
               input.form-control(name="name", type="textbox")
             .form-group
               label(for="category") Item Category:
               input.form-control(name="category", type="textbox")
             br
             button.btn(type="submit") Add item
   

Ce code étend la mise en page et fournit du contenu pour l’espace réservé **content** que nous avons vu plus haut dans le fichier **layout.jade**.
   
Dans cette mise en page, nous avons créé deux fichiers HTML.

Le premier formulaire contient un tableau pour nos données et un bouton qui permet de mettre à jour des éléments en appelant la méthode **/completetask** de notre contrôleur.
    
Le deuxième formulaire contient deux champs d'entrée et un bouton qui permet de créer un élément en appelant la méthode **/addtask** de notre contrôleur.

Ceci devrait être suffisant pour que notre application puisse fonctionner.

## <a name="_Toc395783181"></a>Étape 6 : exécution locale de l'application
1. Pour tester l’application sur votre ordinateur local, exécutez la commande `npm start` dans le terminal pour démarrer votre application, puis actualisez la page [http://localhost:3000](http://localhost:3000) dans votre de navigateur. La page doit maintenant ressembler à l’image ci-dessous :
   
    ![Capture d'écran de l'application MyTodo List dans une fenêtre de navigateur](./media/sql-api-nodejs-application/cosmos-db-node-js-localhost.png)

    > [!TIP]
    > Si vous recevez une erreur liée à la mise en retrait dans le fichier layout.jade ou index.jade, assurez-vous que les deux premières lignes des deux fichiers sont justifiées à gauche et ne présentent aucun espace. Supprimez tout espace éventuel dans les deux premières lignes, enregistrez les deux fichiers, puis actualisez la fenêtre du navigateur. 

2. Utilisez les champs Élément, Nom d’élément et Catégorie pour saisir une nouvelle tâche, puis cliquez sur **Ajouter des éléments**. Cette opération permet de créer un document présentant ces propriétés dans Azure Cosmos DB. 
3. La page doit se mettre à jour et afficher le nouvel élément créé dans la liste des tâches.
   
    ![Capture d'écran de l'application avec un nouvel élément dans la liste de tâches](./media/sql-api-nodejs-application/cosmos-db-node-js-added-task.png)
4. Pour terminer une tâche, activez simplement la case à cocher dans la colonne Complete, puis cliquez sur **Update tasks**. Cette opération met à jour le document que vous avez déjà créé et le supprime de la vue.

5. Pour arrêter l’application, appuyez sur CTRL + C dans la fenêtre de terminal, puis saisissez **Y** pour arrêter le traitement par lots.

## <a name="_Toc395783182"></a>Étape 7 : Déploiement de votre projet de développement d’application sur Sites Web Azure
1. Si vous ne l'avez pas encore fait, activez un référentiel git pour votre site web Azure. Vous trouverez des instructions sur la marche à suivre dans la rubrique [Déploiement Git local vers Azure App Service](../app-service/app-service-deploy-local-git.md) .
2. Ajoutez votre site web Azure en tant que git distant.
   
        git remote add azure https://username@your-azure-website.scm.azurewebsites.net:443/your-azure-website.git
3. Procédez au déploiement par un envoi au git distant.
   
        git push azure master
4. En quelques secondes, git achève la publication de votre application web et lance un navigateur, dans lequel vous pouvez voir votre réalisation exécutée dans Azure.

    Félicitations ! Vous venez de créer votre première application web Express Node.js avec Azure Cosmos DB et de la publier sur les sites web Azure.

    Si vous souhaitez télécharger ou vous référer à l’application de référence complète de ce didacticiel, vous pouvez la télécharger à partir de [GitHub][GitHub].

## <a name="_Toc395637775"></a>Étapes suivantes

* Vous voulez effectuer un test des performances et de la mise à l’échelle avec Azure Cosmos DB ? Consultez la page [Test des performances et de la mise à l’échelle avec Azure Cosmos DB](performance-testing.md).
* Découvrez comment [surveiller un compte Azure Cosmos DB](monitor-accounts.md).
* Exécutez des requêtes sur notre exemple de dataset dans le [Query Playground](https://www.documentdb.com/sql/demo).
* Parcourez la [documentation Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/).

[Node.js]: http://nodejs.org/
[Git]: http://git-scm.com/
[GitHub]: https://github.com/Azure-Samples/documentdb-node-todo-app

