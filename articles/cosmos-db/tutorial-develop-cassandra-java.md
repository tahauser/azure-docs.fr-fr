---
title: "Azure Cosmos DB : développer avec l’API Cassandra dans Java | Microsoft Docs"
description: "Découvrez comment développer avec l’API Cassandra d’Azure Cosmos DB en utilisant Java"
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
tags: 
ms.assetid: 6732d883-835c-481f-98e1-287893530948
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 11/15/2017
ms.author: mimig
ms.custom: mvc
ms.openlocfilehash: 53987e5863d9fc11b4fa377295d198293819269c
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2017
---
# <a name="azure-cosmosdb-develop-with-the-cassandra-api-in-java"></a>Azure Cosmos DB : développer avec l’API Cassandra dans Java

Azure Cosmos DB est le service de base de données multi-modèle de Microsoft distribué à l’échelle mondiale. Rapidement, vous avez la possibilité de créer et d’interroger des documents, des paires clé/valeur, et des bases de données orientées graphe, profitant tous de la distribution à l’échelle mondiale et des capacités de mise à l’échelle horizontale au cœur d’Azure Cosmos DB. 

Ce didacticiel montre comment créer un compte Azure Cosmos DB à l’aide du Portail Azure, puis une table Cassandra (sql-api-partition-data.md#partition-keys) à l’aide de [l’API Cassandra](cassandra-introduction.md). En définissant une clé primaire au moment de créer une table, vous préparez votre application et lui permettez d’être mise à l’échelle sans effort, à mesure que le volume de vos données augmente. 

Ce didacticiel décrit les tâches suivantes à l’aide de l’API Cassandra :

> [!div class="checklist"]
> * Création d’un compte Azure Cosmos DB
> * Création d’un espace de clés et d’une table avec une clé primaire
> * Insertion des données
> * Données de requête
> * Vérification des contrats SLA

## <a name="prerequisites"></a>Prérequis

Accédez au programme d’évaluation de l’API Cassandra Azure Cosmos DB. Si vous n’avez pas encore demandé l’accès, [inscrivez-vous maintenant](https://aka.ms/cosmosdb-cassandra-signup).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] Vous pouvez également [essayer Azure Cosmos DB gratuitement](https://azure.microsoft.com/try/cosmosdb/) sans abonnement Azure, ni frais ni engagement.

Par ailleurs : 

* [Java Development Kit (JDK) 1.7+](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
    * Sur Ubuntu, exécutez `apt-get install default-jdk` pour installer le JDK.
    * Veillez à définir la variable d’environnement JAVA_HOME pour qu’elle pointe vers le dossier dans lequel le JDK est installé.
* [Téléchargement](http://maven.apache.org/download.cgi) et [installation](http://maven.apache.org/install.html) d’une archive binaire [Maven](http://maven.apache.org/)
    * Sur Ubuntu, vous pouvez exécuter `apt-get install maven` pour installer Maven.
* [Git](https://www.git-scm.com/)
    * Sur Ubuntu, vous pouvez exécuter `sudo apt-get install git` pour installer Git.

## <a name="create-a-database-account"></a>Création d'un compte de base de données

Pour pouvoir créer une base de données de documents, vous devez créer un compte Cassandra avec Azure Cosmos DB.

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../../includes/cosmos-db-create-dbaccount-cassandra.md)]

## <a name="clone-the-sample-application"></a>Clonage de l’exemple d’application

À présent, travaillons sur le code. Nous allons maintenant cloner une application Cassandra à partir de GitHub, configurer la chaîne de connexion et l’exécuter. Vous verrez combien il est facile de travailler par programmation avec des données. 

1. Ouvrez une fenêtre de terminal git comme Git Bash et utilisez la commande `cd` pour accéder à un dossier d’installation pour l’exemple d’application. 

    ```bash
    cd "C:\git-samples"
    ```

2. Exécutez la commande suivante pour cloner l’exemple de référentiel : Cette commande crée une copie de l’exemple d’application sur votre ordinateur.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started.git
    ```

## <a name="review-the-code"></a>Vérifier le code

Cette étape est facultative. Pour savoir comment les ressources de base de données sont créées dans le code, vous pouvez examiner les extraits de code suivants. Sinon, vous pouvez passer à l’étape [Mise à jour de votre chaîne de connexion](#update-your-connection-string). Ces extraits de code sont tous extraits du fichier src/main/java/com/azure/cosmosdb/cassandra/util/CassandraUtils.java.  

* L’hôte, le port, le nom d’utilisateur, le mot de passe et les options SSL de Cassandra sont définis. Les informations sur la chaîne de connexion viennent de la page Chaîne de connexion du portail Azure.

   ```java
   cluster = Cluster.builder().addContactPoint(cassandraHost).withPort(cassandraPort).withCredentials(cassandraUsername, cassandraPassword).withSSL(sslOptions).build();
   ```

* Le `cluster` se connecte à l’API Cassandra Azure Cosmos DB et retourne une session à accéder.

    ```java
    return cluster.connect();
    ```

Les extraits de code suivants sont tous extraits du fichier src/main/java/com/azure/cosmosdb/cassandra/repository/UserRepository.java.

* Créez un espace de clé.

    ```java
    public void createKeyspace() {
        final String query = "CREATE KEYSPACE IF NOT EXISTS uprofile WITH replication = {'class': 'SimpleStrategy', 'replication_factor': '3' } ";
        session.execute(query);
        LOGGER.info("Created keyspace 'uprofile'");
    }
    ```

* Créez une table.

   ```java
   public void createTable() {
        final String query = "CREATE TABLE IF NOT EXISTS uprofile.user (user_id int PRIMARY KEY, user_name text, user_bcity text)";
        session.execute(query);
        LOGGER.info("Created table 'user'");
   }
   ```

* Insérez des entités utilisateur à l’aide d’un objet d’instruction préparé.

    ```java
    public PreparedStatement prepareInsertStatement() {
        final String insertStatement = "INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (?,?,?)";
        return session.prepare(insertStatement);
    }

    public void insertUser(PreparedStatement statement, int id, String name, String city) {
        BoundStatement boundStatement = new BoundStatement(statement);
        session.execute(boundStatement.bind(id, name, city));
    }
    ```

* Faites une requête pour obtenir les informations de tous les utilisateurs.

    ```java
   public void selectAllUsers() {
        final String query = "SELECT * FROM uprofile.user";
        List<Row> rows = session.execute(query).all();

        for (Row row : rows) {
            LOGGER.info("Obtained row: {} | {} | {} ", row.getInt("user_id"), row.getString("user_name"), row.getString("user_bcity"));
        }
    }
    ```

* Faites une requête pour obtenir les informations d’un seul utilisateur.

    ```java
    public void selectUser(int id) {
        final String query = "SELECT * FROM uprofile.user where user_id = 3";
        Row row = session.execute(query).one();

        LOGGER.info("Obtained row: {} | {} | {} ", row.getInt("user_id"), row.getString("user_name"), row.getString("user_bcity"));
    }
    ```

## <a name="update-your-connection-string"></a>Mise à jour de votre chaîne de connexion

Maintenant, retournez dans le portail Azure afin d’obtenir les informations de votre chaîne de connexion et de les copier dans l’application. Cette opération permet à votre application de communiquer avec votre base de données hébergée.

1. Dans le [portail Azure](http://portal.azure.com/), cliquez sur **Chaîne de connexion**. 

    ![Afficher et copier un nom d’utilisateur depuis la page Chaîne de connexion du portail Azure](./media/tutorial-develop-cassandra-java/keys.png)

2. Utilisez le ![bouton Copier](./media/tutorial-develop-cassandra-java/copy.png) à droite de l’écran pour copier la valeur POINT DE CONTACT.

3. Ouvrez le fichier `config.properties` du dossier C:\git-samples\azure-cosmosdb-cassandra-java-getting-started\java-examples\src\main\resources. 

3. Collez la valeur POINT DE CONTACT à partir du portail sur `<Cassandra endpoint host>` à la ligne 2.

    La ligne 2 du fichier config.properties doit désormais ressembler à 

    `cassandra_host=cosmos-db-quickstarts.documents.azure.com`

3. Revenez au portail et copiez la valeur NOM D’UTILISATEUR. Collez la valeur NOM D’UTILISATEUR à partir du portail sur `<cassandra endpoint username>` à la ligne 4.

    La ligne 4 du fichier config.properties doit désormais ressembler à 

    `cassandra_username=cosmos-db-quickstart`

4. Revenez au portail et copiez la valeur MOT DE PASSE. Collez la valeur MOT DE PASSE à partir du portail sur `<cassandra endpoint password>` à la ligne 5.

    La ligne 5 du fichier config.properties doit désormais ressembler à 

    `cassandra_password=2Ggkr662ifxz2Mg...==`

5. Sur la ligne 6, si vous voulez utiliser un certificat SSL spécifique, remplacez `<SSL key store file location>` par l’emplacement du certificat SSL. Si aucune valeur n’est renseignée, c’est le certificat JDK installé dans <JAVA_HOME>/jre/lib/security/cacerts qui est utilisé. 

6. Si vous avez modifié la ligne 6 pour utiliser un certificat SSL spécifique, mettez à jour la ligne 7 pour utiliser le mot de passe de ce certificat. 

7. Enregistrez le fichier config.properties.

## <a name="run-the-app"></a>Exécution de l'application

1. Dans la fenêtre de terminal git, exécutez la commande `cd` pour accéder au dossier azure-cosmosdb-cassandra-java-getting-started\java-examples.

    ```git
    cd "C:\git-samples\azure-cosmosdb-cassandra-java-getting-started\java-examples"
    ```

2. Dans la fenêtre de terminal git, exécutez la commande suivante pour générer le fichier cosmosdb-cassandra-examples.jar.

    ```git
    mvn clean install
    ```

3. Dans la fenêtre de terminal git, exécutez la commande suivante pour démarrer l’application Java.

    ```git
    java -cp target/cosmosdb-cassandra-examples.jar com.azure.cosmosdb.cassandra.examples.UserProfile
    ```

    La fenêtre de terminal affiche des notifications indiquant que l’espace de clé et la table sont créés. Elle sélectionne et retourne ensuite tous les utilisateurs dans la table et affiche les sorties, avant de sélectionner une rangée par ID et d’afficher sa valeur.  
    
    Vous pouvez dès à présent revenir à l’Explorateur de données et voir la requête, modifier et travailler avec ces nouvelles données. 

## <a name="review-slas-in-the-azure-portal"></a>Vérification des contrats SLA dans le portail Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>étapes suivantes

Dans ce guide de démarrage rapide, vous avez appris comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Création d’un compte Azure Cosmos DB
> * Création d’un espace de clés et d’une table avec une clé primaire
> * Insertion des données
> * Données de requête
> * Vérification des contrats SLA

Vous pouvez maintenant importer des données supplémentaires dans votre collection Azure Cosmos DB. 

> [!div class="nextstepaction"]
> [Importer des données Cassandra dans Azure Cosmos DB](cassandra-import-data.md)
