---
title: "Se connecter à Azure Database pour MySQL à l’aide de Ruby"
description: "Ce guide de démarrage rapide fournit plusieurs exemples de code Ruby que vous pouvez utiliser pour vous connecter et interroger des données de la base de données Azure pour MySQL."
services: mysql
author: jasonwhowell
ms.author: jasonh
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.custom: mvc
ms.devlang: ruby
ms.topic: quickstart
ms.date: 02/28/2018
ms.openlocfilehash: 84898a5bc3afd21aa6b922b4074fd09b4315dd6e
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2018
---
# <a name="azure-database-for-mysql-use-ruby-to-connect-and-query-data"></a>Base de données Azure pour MySQL : Utilisation de Ruby pour vous connecter et interroger des données
Ce guide de démarrage rapide vous explique comment vous connecter à une base de données Azure pour MySQL via une application [Ruby](https://www.ruby-lang.org) et le gem [mysql2](https://rubygems.org/gems/mysql2), à partir de plateformes Windows, Ubuntu Linux et Mac. Il détaille l’utilisation d’instructions SQL pour interroger la base de données, la mettre à jour, y insérer des données ou en supprimer. Cette rubrique suppose que vous connaissez les bases du développement à l’aide de Ruby, et que vous ne savez pas utiliser la base de données Azure pour MySQL.

## <a name="prerequisites"></a>Prérequis
Ce guide de démarrage rapide s’appuie sur les ressources créées dans l’un de ces guides :
- [Créer un serveur de base de données Azure pour MySQL à l’aide du Portail Azure](./quickstart-create-mysql-server-database-using-azure-portal.md)
- [Création d’un serveur Azure Database pour MySQL à l’aide de la CLI Azure](./quickstart-create-mysql-server-database-using-azure-cli.md)

## <a name="install-ruby"></a>Installer Ruby
Installez Ruby, Gem et la bibliothèque MySQL2 sur votre propre ordinateur. 

### <a name="windows"></a>Windows
1. Téléchargez et installez la version 2.3 de [Ruby](http://rubyinstaller.org/downloads/).
2. Démarrez une nouvelle invite de commandes (cmd) à partir du menu Démarrer.
3. Basculez dans le répertoire Ruby pour la version 2.3. `cd c:\Ruby23-x64\bin`
4. Testez l’installation de Ruby en exécutant la commande `ruby -v` pour voir la version installée.
5. Testez l’installation de Gem en exécutant la commande `gem -v` pour voir la version installée.
6. Compilez le module Mysql2 pour Ruby à l’aide de Gem, en exécutant la commande `gem install mysql2`.

### <a name="macos"></a>MacOS
1. Installez Ruby à l’aide de Homebrew, en exécutant la commande `brew install ruby`. Pour accéder à d’autres options d’installation, consultez la [documentation d’installation](https://www.ruby-lang.org/en/documentation/installation/#homebrew) de Ruby.
2. Testez l’installation de Ruby en exécutant la commande `ruby -v` pour voir la version installée.
3. Testez l’installation de Gem en exécutant la commande `gem -v` pour voir la version installée.
4. Compilez le module Mysql2 pour Ruby à l’aide de Gem, en exécutant la commande `gem install mysql2`.

### <a name="linux-ubuntu"></a>Linux (Ubuntu)
1. Installez Ruby en exécutant la commande `sudo apt-get install ruby-full`. Pour accéder à d’autres options d’installation, consultez la [documentation d’installation](https://www.ruby-lang.org/en/documentation/installation/) de Ruby.
2. Testez l’installation de Ruby en exécutant la commande `ruby -v` pour voir la version installée.
3. Installez les dernières mises à jour de Gem en exécutant la commande `sudo gem update --system`.
4. Testez l’installation de Gem en exécutant la commande `gem -v` pour voir la version installée.
5. Installez les outils de compilation gcc, make, etc. en exécutant la commande `sudo apt-get install build-essential`.
6. Installez les bibliothèques de développeur de client MySQL en exécutant la commande `sudo apt-get install libmysqlclient-dev`.
7. Compilez le module mysql2 pour Ruby à l’aide de Gem, en exécutant la commande `sudo gem install mysql2`.

## <a name="get-connection-information"></a>Obtenir des informations de connexion
Obtenez les informations requises pour vous connecter à la base de données Azure pour MySQL. Vous devez disposer du nom de serveur complet et des informations d’identification.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Dans le menu de gauche du portail Azure, cliquez sur **Toutes les ressources**, puis recherchez le serveur que vous venez de créer, par exemple **mydemoserver**.
3. Cliquez sur le nom du serveur.
4. Dans le panneau **Vue d’ensemble** du serveur, notez le **nom du serveur** et le **nom de connexion de l’administrateur du serveur**. Si vous oubliez votre mot de passe, vous pouvez également le réinitialiser dans ce panneau.
 ![Nom du serveur de base de données Azure pour MySQL](./media/connect-ruby/1_server-overview-name-login.png)

## <a name="run-ruby-code"></a>Exécuter le code Ruby 
1. Collez le code Ruby des sections ci-dessous dans les fichiers textes puis enregistrez les fichiers dans un dossier de projet avec l’extension de fichier .rb, tel que `C:\rubymysql\createtable.rb` ou `/home/username/rubymysql/createtable.rb`.
2. Pour exécuter le code, lancez l’invite de commandes ou l’interpréteur de commandes Bash. Basculez dans votre dossier de projet `cd rubymysql`.
3. Tapez ensuite la commande Ruby suivie du nom de fichier, tel que `ruby createtable.rb` pour exécuter l’application.
4. Avec le système d’exploitation Windows, si l’application Ruby n’est pas dans votre variable d’environnement de chemin d’accès, vous devrez peut-être utiliser le chemin d’accès complet pour lancer l’application de nœud, tel que `"c:\Ruby23-x64\bin\ruby.exe" createtable.rb`

## <a name="connect-and-create-a-table"></a>Se connecter et créer une table
Utilisez le code suivant pour vous connecter et créer une table à l’aide de l’instruction **CREATE TABLE**, suivie des instructions SQL **INSERT INTO** pour ajouter des lignes à la table.

Le code utilise une classe [mysql2::client](http://www.rubydoc.info/gems/mysql2/0.4.8) de méthode .new() pour se connecter à la base de données Azure pour MySQL. Ensuite, il appelle plusieurs fois la méthode [query()](http://www.rubydoc.info/gems/mysql2/0.4.8#Usage) pour exécuter les commandes DROP, CREATE TABLE et INSERT INTO. Ensuite, il appelle la méthode [close()](http://www.rubydoc.info/gems/mysql2/0.4.8/Mysql2/Client#close-instance_method) pour fermer la connexion, avant de s’arrêter.

Remplacez les chaînes `host`, `database`, `username` et `password` par vos propres valeurs. 
```ruby
require 'mysql2'

begin
    # Initialize connection variables.
    host = String('mydemoserver.mysql.database.azure.com')
    database = String('quickstartdb')
    username = String('myadmin@mydemoserver')
    password = String('yourpassword')

    # Initialize connection object.
    client = Mysql2::Client.new(:host => host, :username => username, :database => database, :password => password)
    puts 'Successfully created connection to database.'

    # Drop previous table of same name if one exists
    client.query('DROP TABLE IF EXISTS inventory;')
    puts 'Finished dropping table (if existed).'

    # Drop previous table of same name if one exists.
    client.query('CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);')
    puts 'Finished creating table.'

    # Insert some data into table.
    client.query("INSERT INTO inventory VALUES(1, 'banana', 150)")
    client.query("INSERT INTO inventory VALUES(2, 'orange', 154)")
    client.query("INSERT INTO inventory VALUES(3, 'apple', 100)")
    puts 'Inserted 3 rows of data.'

# Error handling
rescue Exception => e
    puts e.message

# Cleanup
ensure
    client.close if client
    puts 'Done.'
end
```

## <a name="read-data"></a>Lire les données
Utilisez le code suivant pour vous connecter et lire des données à l’aide d’une instruction SQL **SELECT**. 

Le code utilise une méthode [mysql2::client](http://www.rubydoc.info/gems/mysql2/0.4.8) class.new() pour se connecter à la base de données Azure pour MySQL. Ensuite, il appelle la méthode [query()](http://www.rubydoc.info/gems/mysql2/0.4.8#Usage) pour exécuter les commandes SELECT. Ensuite, il appelle la méthode [close()](http://www.rubydoc.info/gems/mysql2/0.4.8/Mysql2/Client#close-instance_method) pour fermer la connexion, avant de s’arrêter.

Remplacez les chaînes `host`, `database`, `username` et `password` par vos propres valeurs. 

```ruby
require 'mysql2'

begin
    # Initialize connection variables.
    host = String('mydemoserver.mysql.database.azure.com')
    database = String('quickstartdb')
    username = String('myadmin@mydemoserver')
    password = String('yourpassword')

    # Initialize connection object.
    client = Mysql2::Client.new(:host => host, :username => username, :database => database, :password => password)
    puts 'Successfully created connection to database.'

    # Read data
    resultSet = client.query('SELECT * from inventory;')
    resultSet.each do |row|
        puts 'Data row = (%s, %s, %s)' % [row['id'], row['name'], row['quantity']]
    end
    puts 'Read ' + resultSet.count.to_s + ' row(s).'

# Error handling
rescue Exception => e
    puts e.message

# Cleanup
ensure
    client.close if client
    puts 'Done.'
end
```

## <a name="update-data"></a>Mettre à jour des données
Utilisez le code suivant pour vous connecter et mettre à jour les données à l’aide d’une instruction SQL **UPDATE**.

Le code utilise une classe [mysql2::client](http://www.rubydoc.info/gems/mysql2/0.4.8) de méthode .new() pour se connecter à la base de données Azure pour MySQL. Ensuite, il appelle la méthode [query()](http://www.rubydoc.info/gems/mysql2/0.4.8#Usage) pour exécuter les commandes UPDATE. Ensuite, il appelle la méthode [close()](http://www.rubydoc.info/gems/mysql2/0.4.8/Mysql2/Client#close-instance_method) pour fermer la connexion, avant de s’arrêter.

Remplacez les chaînes `host`, `database`, `username` et `password` par vos propres valeurs. 

```ruby
require 'mysql2'

begin
    # Initialize connection variables.
    host = String('mydemoserver.mysql.database.azure.com')
    database = String('quickstartdb')
    username = String('myadmin@mydemoserver')
    password = String('yourpassword')

    # Initialize connection object.
    client = Mysql2::Client.new(:host => host, :username => username, :database => database, :password => password)
    puts 'Successfully created connection to database.'

    # Update data
   client.query('UPDATE inventory SET quantity = %d WHERE name = %s;' % [200, '\'banana\''])
   puts 'Updated 1 row of data.'

# Error handling
rescue Exception => e
    puts e.message

# Cleanup
ensure
    client.close if client
    puts 'Done.'
end
```


## <a name="delete-data"></a>Suppression de données
Utilisez le code suivant pour vous connecter et lire les données à l’aide d’une instruction SQL **DELETE**. 

Le code utilise une classe [mysql2::client](http://www.rubydoc.info/gems/mysql2/0.4.8) de méthode .new() pour se connecter à la base de données Azure pour MySQL. Ensuite, il appelle la méthode [query()](http://www.rubydoc.info/gems/mysql2/0.4.8#Usage) pour exécuter les commandes DELETE. Ensuite, il appelle la méthode [close()](http://www.rubydoc.info/gems/mysql2/0.4.8/Mysql2/Client#close-instance_method) pour fermer la connexion, avant de s’arrêter.

Remplacez les chaînes `host`, `database`, `username` et `password` par vos propres valeurs. 

```ruby
require 'mysql2'

begin
    # Initialize connection variables.
    host = String('mydemoserver.mysql.database.azure.com')
    database = String('quickstartdb')
    username = String('myadmin@mydemoserver')
    password = String('yourpassword')

    # Initialize connection object.
    client = Mysql2::Client.new(:host => host, :username => username, :database => database, :password => password)
    puts 'Successfully created connection to database.'

    # Delete data
    resultSet = client.query('DELETE FROM inventory WHERE name = %s;' % ['\'orange\''])
    puts 'Deleted 1 row.'

# Error handling
rescue Exception => e
    puts e.message

# Cleanup
ensure
    client.close if client
    puts 'Done.'
end
```

## <a name="next-steps"></a>étapes suivantes
> [!div class="nextstepaction"]
> [Migration de votre base de données PostgreSQL par exportation et importation](./concepts-migrate-import-export.md)
