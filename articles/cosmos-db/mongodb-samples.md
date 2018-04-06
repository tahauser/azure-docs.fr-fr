---
title: Utiliser les API MongoDB pour générer une application Azure Cosmos DB | Microsoft Docs
description: Un didacticiel qui crée une base de données en ligne à l’aide des API Azure Cosmos DB pour MongoDB.
keywords: exemples mongodb
services: cosmos-db
author: AndrewHoh
manager: jhubbard
editor: ''
documentationcenter: ''
ms.assetid: fb38bc53-3561-487d-9e03-20f232319a87
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2018
ms.author: anhoh
ms.openlocfilehash: 1571ed8bc3146a6351d0010a9f072cad986d6dc7
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2018
---
# <a name="build-an-azure-cosmos-db-api-for-mongodb-app-using-nodejs"></a>Générer une application Azure Cosmos DB : API pour MongoDB à l’aide de Node.js
> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [Node.js pour MongoDB](mongodb-samples.md)
> * [Node.JS](sql-api-nodejs-get-started.md)
> * [C++](sql-api-cpp-get-started.md)
>  
>

Cet exemple vous montre comment générer une application console Azure Cosmos DB : API pour MongoDB à l’aide de Node.js.

Pour utiliser cet exemple, vous devez :

* [Créez](create-mongodb-dotnet.md#create-account) un compte Azure Cosmos DB : API pour MongoDB.
* Récupérer les informations de [chaîne de connexion](connect-mongodb-account.md) MongoDB.

## <a name="create-the-app"></a>Création de l'application

1. Créez un fichier *app.js* et copiez puis collez le code ci-dessous.

    ```nodejs
    var MongoClient = require('mongodb').MongoClient;
    var assert = require('assert');
    var ObjectId = require('mongodb').ObjectID;
    var url = 'mongodb://<username>:<password>@<endpoint>.documents.azure.com:10255/?ssl=true';

    var insertDocument = function(db, callback) {
    db.collection('families').insertOne( {
            "id": "AndersenFamily",
            "lastName": "Andersen",
            "parents": [
                { "firstName": "Thomas" },
                { "firstName": "Mary Kay" }
            ],
            "children": [
                { "firstName": "John", "gender": "male", "grade": 7 }
            ],
            "pets": [
                { "givenName": "Fluffy" }
            ],
            "address": { "country": "USA", "state": "WA", "city": "Seattle" }
        }, function(err, result) {
        assert.equal(err, null);
        console.log("Inserted a document into the families collection.");
        callback();
    });
    };
    
    var findFamilies = function(db, callback) {
    var cursor =db.collection('families').find( );
    cursor.each(function(err, doc) {
        assert.equal(err, null);
        if (doc != null) {
            console.dir(doc);
        } else {
            callback();
        }
    });
    };
    
    var updateFamilies = function(db, callback) {
    db.collection('families').updateOne(
        { "lastName" : "Andersen" },
        {
            $set: { "pets": [
                { "givenName": "Fluffy" },
                { "givenName": "Rocky"}
            ] },
            $currentDate: { "lastModified": true }
        }, function(err, results) {
        console.log(results);
        callback();
    });
    };
    
    var removeFamilies = function(db, callback) {
    db.collection('families').deleteMany(
        { "lastName": "Andersen" },
        function(err, results) {
            console.log(results);
            callback();
        }
    );
    };
    
    MongoClient.connect(url, function(err, client) {
    assert.equal(null, err);
    var db = client.db('familiesdb');
    insertDocument(db, function() {
        findFamilies(db, function() {
        updateFamilies(db, function() {
            removeFamilies(db, function() {
                client.close();
            });
        });
        });
    });
    });
    ```
    
    **Facultatif** : si vous utilisez le **pilote MongoDB.Node.js 2.2**, remplacez l’extrait de code suivant :

    Ressource d’origine :

    ```nodejs
    MongoClient.connect(url, function(err, client) {
    assert.equal(null, err);
    var db = client.db('familiesdb');
    insertDocument(db, function() {
        findFamilies(db, function() {
        updateFamilies(db, function() {
            removeFamilies(db, function() {
                client.close();
            });
        });
        });
    });
    });
    ```
    
    À remplacer par :

    ```nodejs
    MongoClient.connect(url, function(err, db) {
    assert.equal(null, err);
    insertDocument(db, function() {
        findFamilies(db, function() {
        updateFamilies(db, function() {
            removeFamilies(db, function() {
                db.close();
            });
        });
        });
    });
    });
    ```
    
2. Modifiez les variables suivantes dans le fichier *app.js* selon les paramètres de votre compte (découvrez comment rechercher votre [chaîne de connexion](connect-mongodb-account.md)) :

    > [!IMPORTANT]
    > Le **pilote MongoDB Node.js 3.0** nécessite l’encodage des caractères spéciaux dans le mot de passe de Cosmos DB. Veillez à encoder les caractères '=' sous la forme % 3D
    >
    > Exemple : le mot de passe *jm1HbNdLg5zxEuyD86ajvINRFrFCUX0bIWP15ATK3BvSv==* est encodé *jm1HbNdLg5zxEuyD86ajvINRFrFCUX0bIWP15ATK3BvSv%3D%3D*
    >
    > Le **pilote MongoDB Node.js 2.2** ne nécessite pas l’encodage des caractères spéciaux dans le mot de passe de Cosmos DB.
    >
    >
   
    ```nodejs
    var url = 'mongodb://<endpoint>:<password>@<endpoint>.documents.azure.com:10255/?ssl=true';
    ```
     
3. Ouvrez votre terminal préféré, exécutez **npm install mongodb --save**, puis exécutez votre application avec **node app.js**

## <a name="next-steps"></a>Étapes suivantes
* Découvrez comment [utiliser MongoChef](mongodb-mongochef.md) avec votre compte Azure Cosmos DB : API pour MongoDB.
