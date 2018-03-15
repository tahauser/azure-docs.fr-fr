---
title: "Se connecter à Azure Database pour MySQL à l’aide de Java"
description: "Ce guide de démarrage rapide fournit plusieurs exemples de code Java, que vous pouvez utiliser pour vous connecter et interroger des données à partir de la base de données Azure pour MySQL."
services: mysql
author: jasonwhowell
ms.author: jasonh
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.custom: mvc, devcenter
ms.topic: quickstart
ms.devlang: java
ms.date: 02/28/2018
ms.openlocfilehash: efc1fd07f09bd0bae3c21b9d63c04020abc7832e
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2018
---
# <a name="azure-database-for-mysql-use-java-to-connect-and-query-data"></a>Base de données Azure pour MySQL : Utilisation de Java pour se connecter et interroger des données
Ce guide de démarrage rapide vous explique comment vous connecter à une base de données Azure Database pour MySQL en utilisant une application Java et le pilote JDBC [MySQL Connector/J](https://dev.mysql.com/downloads/connector/j/). Il détaille l’utilisation d’instructions SQL pour interroger la base de données, la mettre à jour, y insérer des données ou en supprimer. Cet article part du principe que vous connaissez les bases du développement à l’aide de Java et que vous ne savez pas utiliser Azure Database pour MySQL.

La page [Exemples de connecteur MySQL](https://dev.mysql.com/doc/connector-j/5.1/en/connector-j-examples.html) comporte plusieurs autres exemples et exemples de code.

## <a name="prerequisites"></a>Prérequis
1. Ce guide de démarrage rapide s’appuie sur les ressources créées dans l’un de ces guides :
   - [Créer un serveur de base de données Azure pour MySQL à l’aide du Portail Azure](./quickstart-create-mysql-server-database-using-azure-portal.md)
   - [Création d’un serveur de base de données Azure pour MySQL à l’aide d’Azure CLI](./quickstart-create-mysql-server-database-using-azure-cli.md)

2. Vous assurer que votre base de données Azure pour la sécurité de connexion MySQL est configurée avec le pare-feu ouvert et les paramètres SSL adaptés à votre application pour une connexion réussie.

3. Obtenez le connecteur MySQL/J à l’aide d’une des approches suivantes :
   - Utilisez le package Maven [mysql-connector-java](http://search.maven.org/#search%7Cga%7C1%7Cg%3A%22mysql%22%20AND%20a%3A%22mysql-connector-java%22) pour inclure la [dépendance mysql](https://mvnrepository.com/artifact/mysql/mysql-connector-java/5.1.6) dans le fichier POM de votre projet.
   - Téléchargez le pilote JDBC [MySQL Connector/J](https://dev.mysql.com/downloads/connector/j/) et incluez le fichier jar JDBC (par exemple, mysql-connector-java-5.1.42-bin.jar) dans le chemin de classe de votre application. Si vous rencontrez un problème avec les chemins de classe, consultez la documentation de votre environnement pour obtenir des détails sur le chemin d’accès de classe, comme [Apache Tomcat](https://tomcat.apache.org/tomcat-7.0-doc/class-loader-howto.html) ou [Java SE](http://docs.oracle.com/javase/7/docs/technotes/tools/windows/classpath.html)

## <a name="get-connection-information"></a>Obtenir des informations de connexion
Obtenez les informations requises pour vous connecter à la base de données Azure pour MySQL. Vous devez disposer du nom de serveur complet et des informations d’identification.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Dans le menu de gauche du portail Azure, cliquez sur **Toutes les ressources**, puis recherchez le serveur que vous venez de créer, par exemple **mydemoserver**.
3. Cliquez sur le nom du serveur.
4. Dans le panneau **Vue d’ensemble** du serveur, notez le **nom du serveur** et le **nom de connexion de l’administrateur du serveur**. Si vous oubliez votre mot de passe, vous pouvez également le réinitialiser dans ce panneau.
 ![Nom du serveur de base de données Azure pour MySQL](./media/connect-java/1_server-overview-name-login.png)

## <a name="connect-create-table-and-insert-data"></a>Se connecter, créer des tables et insérer des données
Utilisez le code suivant pour vous connecter et charger les données à l’aide d’une instruction SQL **INSERT**. La méthode [getConnection()](https://dev.mysql.com/doc/connector-j/5.1/en/connector-j-usagenotes-connect-drivermanager.html) permet de se connecter à MySQL. Les méthodes [createStatement()](https://dev.mysql.com/doc/connector-j/5.1/en/connector-j-usagenotes-statements.html) et execute() permettent de supprimer et de créer la table. L’objet prepareStatement permet de générer les commandes d’insertion, en se servant des méthodes setString() et setInt() pour lier les valeurs des paramètres. La méthode executeUpdate() exécute la commande pour chaque ensemble de paramètres afin d’insérer les valeurs. 

Remplacez les paramètres d’hôte, de la base de données, d’utilisateur et du mot de passe par les valeurs spécifiées lors de la création de votre serveur et de votre base de données.

```java
import java.sql.*;
import java.util.Properties;

public class CreateTableInsertRows {

    public static void main (String[] args)  throws Exception
    {
        // Initialize connection variables. 
        String host = "mydemoserver.mysql.database.azure.com";
        String database = "quickstartdb";
        String user = "myadmin@mydemoserver";
        String password = "<server_admin_password>";

        // check that the driver is installed
        try
        {
            Class.forName("com.mysql.jdbc.Driver");
        }
        catch (ClassNotFoundException e)
        {
            throw new ClassNotFoundException("MySQL JDBC driver NOT detected in library path.", e);
        }

        System.out.println("MySQL JDBC driver detected in library path.");

        Connection connection = null;

        // Initialize connection object
        try
        {
            String url = String.format("jdbc:mysql://%s/%s", host, database);

            // Set connection properties.
            Properties properties = new Properties();
            properties.setProperty("user", user);
            properties.setProperty("password", password);
            properties.setProperty("useSSL", "true");
            properties.setProperty("verifyServerCertificate", "true");
            properties.setProperty("requireSSL", "false");

            // get connection
            connection = DriverManager.getConnection(url, properties);
        }
        catch (SQLException e)
        {
            throw new SQLException("Failed to create connection to database.", e);
        }
        if (connection != null) 
        { 
            System.out.println("Successfully created connection to database.");
        
            // Perform some SQL queries over the connection.
            try
            {
                // Drop previous table of same name if one exists.
                Statement statement = connection.createStatement();
                statement.execute("DROP TABLE IF EXISTS inventory;");
                System.out.println("Finished dropping table (if existed).");
    
                // Create table.
                statement.execute("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);");
                System.out.println("Created table.");
                
                // Insert some data into table.
                int nRowsInserted = 0;
                PreparedStatement preparedStatement = connection.prepareStatement("INSERT INTO inventory (name, quantity) VALUES (?, ?);");
                preparedStatement.setString(1, "banana");
                preparedStatement.setInt(2, 150);
                nRowsInserted += preparedStatement.executeUpdate();

                preparedStatement.setString(1, "orange");
                preparedStatement.setInt(2, 154);
                nRowsInserted += preparedStatement.executeUpdate();

                preparedStatement.setString(1, "apple");
                preparedStatement.setInt(2, 100);
                nRowsInserted += preparedStatement.executeUpdate();
                System.out.println(String.format("Inserted %d row(s) of data.", nRowsInserted));
    
                // NOTE No need to commit all changes to database, as auto-commit is enabled by default.
    
            }
            catch (SQLException e)
            {
                throw new SQLException("Encountered an error when executing given sql statement.", e);
            }       
        }
        else {
            System.out.println("Failed to create connection to database.");
        }
        System.out.println("Execution finished.");
    }
}

```

## <a name="read-data"></a>Lire les données
Utilisez le code suivant pour lire des données à l’aide d’une instruction SQL **SELECT**. La méthode [getConnection()](https://dev.mysql.com/doc/connector-j/5.1/en/connector-j-usagenotes-connect-drivermanager.html) permet de se connecter à MySQL. Les méthodes [createStatement()](https://dev.mysql.com/doc/connector-j/5.1/en/connector-j-usagenotes-statements.html) et executeQuery() permettent de se connecter et d’exécuter l’instruction sélectionnée. Les résultats sont traités à l’aide de l’objet [ResultSet](https://docs.oracle.com/javase/tutorial/jdbc/basics/retrieving.html). 

Remplacez les paramètres d’hôte, de la base de données, d’utilisateur et du mot de passe par les valeurs spécifiées lors de la création de votre serveur et de votre base de données.

```java
import java.sql.*;
import java.util.Properties;

public class ReadTable {

    public static void main (String[] args)  throws Exception
    {
        // Initialize connection variables.
        String host = "mydemoserver.mysql.database.azure.com";
        String database = "quickstartdb";
        String user = "myadmin@mydemoserver";
        String password = "<server_admin_password>";

        // check that the driver is installed
        try
        {
            Class.forName("com.mysql.jdbc.Driver");
        }
        catch (ClassNotFoundException e)
        {
            throw new ClassNotFoundException("MySQL JDBC driver NOT detected in library path.", e);
        }

        System.out.println("MySQL JDBC driver detected in library path.");

        Connection connection = null;

        // Initialize connection object
        try
        {
            String url = String.format("jdbc:mysql://%s/%s", host, database);

            // Set connection properties.
            Properties properties = new Properties();
            properties.setProperty("user", user);
            properties.setProperty("password", password);
            properties.setProperty("useSSL", "true");
            properties.setProperty("verifyServerCertificate", "true");
            properties.setProperty("requireSSL", "false");
            
            // get connection
            connection = DriverManager.getConnection(url, properties);
        }
        catch (SQLException e)
        {
            throw new SQLException("Failed to create connection to database", e);
        }
        if (connection != null) 
        { 
            System.out.println("Successfully created connection to database.");
        
            // Perform some SQL queries over the connection.
            try
            {
    
                Statement statement = connection.createStatement();
                ResultSet results = statement.executeQuery("SELECT * from inventory;");
                while (results.next())
                {
                    String outputString = 
                        String.format(
                            "Data row = (%s, %s, %s)",
                            results.getString(1),
                            results.getString(2),
                            results.getString(3));
                    System.out.println(outputString);
                }
            }
            catch (SQLException e)
            {
                throw new SQLException("Encountered an error when executing given sql statement", e);
            }       
        }
        else {
            System.out.println("Failed to create connection to database."); 
        }
        System.out.println("Execution finished.");
    }
}
```

## <a name="update-data"></a>Mettre à jour des données
Utilisez le code suivant pour modifier les données à l’aide d’une instruction SQL **UPDATE**. La méthode [getConnection()](https://dev.mysql.com/doc/connector-j/5.1/en/connector-j-usagenotes-connect-drivermanager.html) permet de se connecter à MySQL. Les méthodes [prepareStatement()](http://docs.oracle.com/javase/tutorial/jdbc/basics/prepared.html) et executeUpdate() permettent de préparer et d’exécuter l’instruction de mise à jour. 

Remplacez les paramètres d’hôte, de la base de données, d’utilisateur et du mot de passe par les valeurs spécifiées lors de la création de votre serveur et de votre base de données.

```java
import java.sql.*;
import java.util.Properties;

public class UpdateTable {
    public static void main (String[] args)  throws Exception
    {
        // Initialize connection variables. 
        String host = "mydemoserver.mysql.database.azure.com";
        String database = "quickstartdb";
        String user = "myadmin@mydemoserver";
        String password = "<server_admin_password>";

        // check that the driver is installed
        try
        {
            Class.forName("com.mysql.jdbc.Driver");
        }
        catch (ClassNotFoundException e)
        {
            throw new ClassNotFoundException("MySQL JDBC driver NOT detected in library path.", e);
        }
        System.out.println("MySQL JDBC driver detected in library path.");

        Connection connection = null;

        // Initialize connection object
        try
        {
            String url = String.format("jdbc:mysql://%s/%s", host, database);
            
            // set up the connection properties
            Properties properties = new Properties();
            properties.setProperty("user", user);
            properties.setProperty("password", password);
            properties.setProperty("useSSL", "true");
            properties.setProperty("verifyServerCertificate", "true");
            properties.setProperty("requireSSL", "false");

            // get connection
            connection = DriverManager.getConnection(url, properties);
        }
        catch (SQLException e)
        {
            throw new SQLException("Failed to create connection to database.", e);
        }
        if (connection != null) 
        { 
            System.out.println("Successfully created connection to database.");
        
            // Perform some SQL queries over the connection.
            try
            {
                // Modify some data in table.
                int nRowsUpdated = 0;
                PreparedStatement preparedStatement = connection.prepareStatement("UPDATE inventory SET quantity = ? WHERE name = ?;");
                preparedStatement.setInt(1, 200);
                preparedStatement.setString(2, "banana");
                nRowsUpdated += preparedStatement.executeUpdate();
                System.out.println(String.format("Updated %d row(s) of data.", nRowsUpdated));
    
                // NOTE No need to commit all changes to database, as auto-commit is enabled by default.
            }
            catch (SQLException e)
            {
                throw new SQLException("Encountered an error when executing given sql statement.", e);
            }       
        }
        else {
            System.out.println("Failed to create connection to database.");
        }
        System.out.println("Execution finished.");
    }
}
```

## <a name="delete-data"></a>Suppression de données
Utilisez le code suivant pour supprimer des données à l’aide d’une instruction SQL **DELETE**. La méthode [getConnection()](https://dev.mysql.com/doc/connector-j/5.1/en/connector-j-usagenotes-connect-drivermanager.html) permet de se connecter à MySQL.  Les méthodes [prepareStatement()](http://docs.oracle.com/javase/tutorial/jdbc/basics/prepared.html) et executeUpdate() permettent de préparer et d’exécuter l’instruction de mise à jour. 

Remplacez les paramètres d’hôte, de la base de données, d’utilisateur et du mot de passe par les valeurs spécifiées lors de la création de votre serveur et de votre base de données.

```java
import java.sql.*;
import java.util.Properties;

public class DeleteTable {
    public static void main (String[] args)  throws Exception
    {
        // Initialize connection variables.
        String host = "mydemoserver.mysql.database.azure.com";
        String database = "quickstartdb";
        String user = "myadmin@mydemoserver";
        String password = "<server_admin_password>";
        
        // check that the driver is installed
        try
        {
            Class.forName("com.mysql.jdbc.Driver");
        }
        catch (ClassNotFoundException e)
        {
            throw new ClassNotFoundException("MySQL JDBC driver NOT detected in library path.", e);
        }

        System.out.println("MySQL JDBC driver detected in library path.");

        Connection connection = null;

        // Initialize connection object
        try
        {
            String url = String.format("jdbc:mysql://%s/%s", host, database);
            
            // set up the connection properties
            Properties properties = new Properties();
            properties.setProperty("user", user);
            properties.setProperty("password", password);
            properties.setProperty("useSSL", "true");
            properties.setProperty("verifyServerCertificate", "true");
            properties.setProperty("requireSSL", "false");
            
            // get connection
            connection = DriverManager.getConnection(url, properties);
        }
        catch (SQLException e)
        {
            throw new SQLException("Failed to create connection to database", e);
        }
        if (connection != null) 
        { 
            System.out.println("Successfully created connection to database.");
        
            // Perform some SQL queries over the connection.
            try
            {
                // Delete some data from table.
                int nRowsDeleted = 0;
                PreparedStatement preparedStatement = connection.prepareStatement("DELETE FROM inventory WHERE name = ?;");
                preparedStatement.setString(1, "orange");
                nRowsDeleted += preparedStatement.executeUpdate();
                System.out.println(String.format("Deleted %d row(s) of data.", nRowsDeleted));
    
                // NOTE No need to commit all changes to database, as auto-commit is enabled by default.
            }
            catch (SQLException e)
            {
                throw new SQLException("Encountered an error when executing given sql statement.", e);
            }       
        }
        else {
            System.out.println("Failed to create connection to database.");
        }
        System.out.println("Execution finished.");
    }
}
```

## <a name="next-steps"></a>Étapes suivantes
La page [Exemples de connecteur MySQL/J](https://dev.mysql.com/doc/connector-j/5.1/en/connector-j-examples.html) comporte plusieurs autres exemples et exemples de code.

> [!div class="nextstepaction"]
> [Migrer une base de données MySQL vers une base de données Azure pour MySQL à l’aide des images mémoire et de la restauration](concepts-migrate-dump-restore.md)
