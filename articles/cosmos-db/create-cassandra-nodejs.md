---
title: "Démarrage rapide : API Cassandra avec Node.js - Azure Cosmos DB | Microsoft Docs"
description: "Ce guide de démarrage rapide montre comment utiliser l’API Cassandra Azure Cosmos DB pour créer une application de profil avec Node.js"
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 4732e57d-32ed-40e2-b148-a8df4ff2630d
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 11/15/2017
ms.author: govindk
ms.openlocfilehash: 76850d6403fc4e87e95f5842b87b258d652c2c35
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2017
---
# <a name="quickstart-build-a-cassandra-app-with-nodejs-and-azure-cosmos-db"></a>Démarrage rapide : Créer une application Cassandra avec Node.js et Azure Cosmos DB

Ce guide de démarrage rapide montre comment utiliser Node.js et [l’API Cassandra](cassandra-introduction.md) Azure Cosmos DB pour créer une application de profil en clonant un exemple de GitHub. Ce guide de démarrage rapide vous montre également comment créer un compte Azure Cosmos DB en utilisant le portail web Azure.

Azure Cosmos DB est le service de base de données multi-modèle de Microsoft distribué à l’échelle mondiale. Vous pouvez rapidement créer et interroger des bases de données de documents, de tables, de paires clé/valeur et de graphes, lesquelles bénéficient toutes des fonctionnalités de distribution mondiale et de mise à l’échelle horizontale d’Azure Cosmos DB. 

## <a name="prerequisites"></a>Composants requis

* Accédez au programme d’évaluation de l’API Cassandra Azure Cosmos DB. Si vous n’avez pas encore demandé l’accès, [inscrivez-vous maintenant](https://aka.ms/cosmosdb-cassandra-signup).
* [Node.js](https://nodejs.org/en/) version v0.10.29 ou ultérieure
* [Git](http://git-scm.com/)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] Vous pouvez également [essayer Azure Cosmos DB gratuitement](https://azure.microsoft.com/try/cosmosdb/) sans abonnement Azure, ni frais ni engagement.


## <a name="create-a-database-account"></a>Création d'un compte de base de données

Pour pouvoir créer une base de données de documents, vous devez créer un compte Cassandra avec Azure Cosmos DB.

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../../includes/cosmos-db-create-dbaccount-cassandra.md)]

## <a name="clone-the-sample-application"></a>Clonage de l’exemple d’application

Nous allons maintenant cloner une application API Cassandra à partir de GitHub, configurer la chaîne de connexion et l’exécuter. Vous pouvez constater à quel point il est facile de travailler par programmation avec des données. 

1. Ouvrez une fenêtre de terminal git comme Git Bash et utilisez la commande `cd` pour accéder à un dossier d’installation pour l’exemple d’application. 

    ```bash
    cd "C:\git-samples"
    ```

2. Exécutez la commande suivante pour cloner l’exemple de référentiel : Cette commande crée une copie de l’exemple d’application sur votre ordinateur.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-cassandra-nodejs-getting-started.git
    ```

## <a name="review-the-code"></a>Examiner le code

Cette étape est facultative. Pour savoir comment les ressources de base de données sont créées dans le code, vous pouvez examiner les extraits de code suivants. Les extraits de code sont tirés du fichier `uprofile.js` dans le dossier C:\git-samples\azure-cosmos-db-cassandra-nodejs-getting-started. Sinon, vous pouvez passer à l’étape [Mise à jour de votre chaîne de connexion](#update-your-connection-string). 

* Le nom d’utilisateur et le mot de passe sont définis à l’aide de la page de chaîne de connexion dans le portail Azure. Le « path\to\cert » fournit le chemin d’un certificat X509. 

   ```nodejs
   var ssl_option = {
        cert : fs.readFileSync("path\to\cert"),
        rejectUnauthorized : true,
        secureProtocol: 'TLSv1_2_method'
        };
   const authProviderLocalCassandra = new cassandra.auth.PlainTextAuthProvider(config.username, config.password);
   ```

* Le `client` est initialisé avec les informations contactPoint. Le contactPoint est récupéré à partir du portail Azure.

    ```nodejs
    const client = new cassandra.Client({contactPoints: [config.contactPoint], authProvider: authProviderLocalCassandra, sslOptions:ssl_option});
    ```

* Le `client` se connecte à l’API Cassandra Azure Cosmos DB.

    ```nodejs
    client.connect(next);
    ```

* Un espace de clés est créé.

    ```nodejs
    function createKeyspace(next) {
        var query = "CREATE KEYSPACE IF NOT EXISTS uprofile WITH replication = {\'class\': \'NetworkTopologyStrategy\', \'datacenter1\' : \'1\' }";
        client.execute(query, next);
        console.log("created keyspace");    
  }
    ```

* Une table est créée.

   ```nodejs
   function createTable(next) {
    var query = "CREATE TABLE IF NOT EXISTS uprofile.user (user_id int PRIMARY KEY, user_name text, user_bcity text)";
        client.execute(query, next);
        console.log("created table");
   },
   ```

* Des entités clé/valeur sont insérées.

    ```nodejs
    ...
       {
          query: 'INSERT INTO  uprofile.user  (user_id, user_name , user_bcity) VALUES (?,?,?)',
          params: [5, 'IvanaV', 'Belgaum', '2017-10-3136']
        }
    ];
    client.batch(queries, { prepare: true}, next);
    ```

* Requête pour obtenir toutes les valeurs de clé.

    ```nodejs
   var query = 'SELECT * FROM uprofile.user';
    client.execute(query, { prepare: true}, function (err, result) {
      if (err) return next(err);
      result.rows.forEach(function(row) {
        console.log('Obtained row: %d | %s | %s ',row.user_id, row.user_name, row.user_bcity);
      }, this);
      next();
    });
    ```  
    
* Requête pour obtenir une paire clé-valeur.

    ```nodejs
    function selectById(next) {
        console.log("\Getting by id");
        var query = 'SELECT * FROM uprofile.user where user_id=1';
        client.execute(query, { prepare: true}, function (err, result) {
        if (err) return next(err);
            result.rows.forEach(function(row) {
            console.log('Obtained row: %d | %s | %s ',row.user_id, row.user_name, row.user_bcity);
        }, this);
        next();
        });
    }
    ```  

## <a name="update-your-connection-string"></a>Mise à jour de votre chaîne de connexion

Maintenant, retournez dans le portail Azure afin d’obtenir les informations de votre chaîne de connexion et de les copier dans l’application. Cette opération permet à votre application de communiquer avec votre base de données hébergée.

1. Dans le [portail Azure](http://portal.azure.com/), cliquez sur **Chaîne de connexion**. 

    Utilisez le ![bouton Copier](./media/create-cassandra-nodejs/copy.png) à droite de l’écran pour copier la valeur supérieure, c’est-à-dire le POINT DE CONTACT.

    ![Affichez et copiez les valeurs POINT DE CONTACT, NOM D’UTILISATEUR et MOT DE PASSE à partir de la page de chaîne de connexion du portail Azure](./media/create-cassandra-nodejs/keys.png)

2. Ouvrez le fichier `config.js` . 

3. Collez la valeur POINT DE CONTACT à partir du portail sur `<FillMEIN>` à la ligne 4.

    La ligne 4 doit maintenant ressembler à 

    `config.contactPoint = "cosmos-db-quickstarts.documents.azure.com:10350"`

4. Copiez la valeur NOM D’UTILISATEUR à partir du portail et collez-la sur `<FillMEIN>` à la ligne 2.

    La ligne 2 doit maintenant ressembler à 

    `config.username = 'cosmos-db-quickstart';`
    
5. Copiez la valeur MOT DE PASSE à partir du portail et collez-la sur `<FillMEIN>` à la ligne 3.

    La ligne 3 doit maintenant ressembler à

    `config.password = '2Ggkr662ifxz2Mg==';`

6. Enregistrez le fichier config.js.
    
## <a name="use-the-x509-certificate"></a>Utiliser le certificat X509 

1. Si vous devez ajouter le certificat racine Baltimore CyberTrust, son numéro de série est 02:00:00:b9 et son empreinte digitale SHA1 est d4:de:20:d0:5e:66:fc:53:fe:1a:50:88:2 c: 78:db:28:52:ca:e4:74. Il peut être téléchargé à partir de https://cacert.omniroot.com/bc2025.crt, enregistré dans un fichier local avec l'extension .cer. 

2. Ouvrez uprofile.js et changez « path\to\cert » pour pointer vers votre nouveau certificat. 

3. Enregistrez uprofile.js. 

## <a name="run-the-app"></a>Exécution de l'application

1. Dans la fenêtre de terminal git, exécutez `npm install` pour installer les modules npm nécessaires.

2. Exécutez `node uprofile.js` pour démarrer votre application Node.

3. Vérifiez que les résultats sont corrects à partir de la ligne de commande.

    ![Consulter et vérifier la sortie](./media/create-cassandra-nodejs/output.png)

    Appuyez sur CTRL + C pour arrêter l’exécution du programme et fermer la fenêtre de console. 

    Vous pouvez maintenant ouvrir l’Explorateur de données dans le portail Azure pour voir la requête, modifier ces nouvelles données et les utiliser. 

    ![Afficher les données dans l’Explorateur de données](./media/create-cassandra-nodejs/data-explorer.png) 

## <a name="review-slas-in-the-azure-portal"></a>Vérification des contrats SLA dans le portail Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez appris à créer un compte Azure Cosmos DB, à créer une collection à l’aide de l’Explorateur de données, et à exécuter une application. Vous pouvez maintenant importer des données supplémentaires à votre compte Cosmos DB. 

> [!div class="nextstepaction"]
> [Importer des données Cassandra dans Azure Cosmos DB](cassandra-import-data.md)


