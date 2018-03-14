---
title: "Se connecter à Azure Database pour PostgreSQL à l’aide de Ruby"
description: "Ce guide de démarrage rapide fournit un exemple de code Ruby, que vous pouvez utiliser pour vous connecter et interroger des données de la base de données Azure pour PostgreSQL."
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.custom: mvc
ms.devlang: ruby
ms.topic: quickstart
ms.date: 02/28/2018
ms.openlocfilehash: 911dcd49273edb202c64d046424418b7db048291
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2018
---
# <a name="azure-database-for-postgresql-use-ruby-to-connect-and-query-data"></a>Base de données Azure pour PostgreSQL : Utilisation de Ruby pour se connecter et interroger des données
Ce guide de démarrage rapide vous explique comment vous connecter à une base de données Azure pour PostgreSQL en utilisant une application [Ruby](https://www.ruby-lang.org). Il détaille l’utilisation d’instructions SQL pour interroger la base de données, la mettre à jour, y insérer des données ou en supprimer. Cet article suppose que vous connaissez les bases du développement via Ruby, et que vous ne savez pas utiliser Azure Database pour PostgreSQL.

## <a name="prerequisites"></a>Prérequis
Ce guide de démarrage rapide s’appuie sur les ressources créées dans l’un de ces guides :
- [Créer une base de données - Portail](quickstart-create-server-database-portal.md)
- [Créer une base de données - Interface de ligne de commande Azure](quickstart-create-server-database-azure-cli.md)

## <a name="install-ruby"></a>Installer Ruby
Installez Ruby sur votre ordinateur. 

### <a name="windows"></a>Windows
- Téléchargez et installez la dernière version de [Ruby](http://rubyinstaller.org/downloads/).
- Sur l’écran de fin du programme d’installation MSI, cochez la case relative à l’exécution de la commande « ridk install » pour installer MSYS2 et la chaîne d’outils de développement. Ensuite, cliquez sur **Terminer** pour lancer le programme d’installation suivant.
- Le programme d’installation RubyInstaller2 pour Windows se lance. Saisissez « 2 » pour installer la mise à jour de la base de données de référentiel MSYS2. Une fois l’opération terminée, lorsque l’invite d’installation s’affiche à nouveau, fermez la fenêtre de commande.
- Démarrez une nouvelle invite de commandes (cmd) à partir du menu Démarrer.
- Testez l’installation de Ruby (`ruby -v`) pour afficher la version installée.
- Testez l’installation de Gem (`gem -v`) pour afficher la version installée.
- Compilez le module PostgreSQL pour Ruby à l’aide de Gem, en exécutant la commande `gem install pg`.

### <a name="macos"></a>MacOS
- Installez Ruby à l’aide de Homebrew, en exécutant la commande `brew install ruby`. Pour accéder à d’autres options d’installation, consultez la [documentation d’installation](https://www.ruby-lang.org/en/documentation/installation/#homebrew) de Ruby.
- Testez l’installation de Ruby (`ruby -v`) pour afficher la version installée.
- Testez l’installation de Gem (`gem -v`) pour afficher la version installée.
- Compilez le module PostgreSQL pour Ruby à l’aide de Gem, en exécutant la commande `gem install pg`.

### <a name="linux-ubuntu"></a>Linux (Ubuntu)
- Installez Ruby en exécutant la commande `sudo apt-get install ruby-full`. Pour accéder à d’autres options d’installation, consultez la [documentation d’installation](https://www.ruby-lang.org/en/documentation/installation/) de Ruby.
- Testez l’installation de Ruby (`ruby -v`) pour afficher la version installée.
- Installez les dernières mises à jour de Gem en exécutant la commande `sudo gem update --system`.
- Testez l’installation de Gem (`gem -v`) pour afficher la version installée.
- Installez les outils de compilation gcc, make, etc. en exécutant la commande `sudo apt-get install build-essential`.
- Installer les bibliothèques PostgreSQL en exécutant la commande `sudo apt-get install libpq-dev`.
- Générez le module pg Ruby à l’aide de Gem en exécutant la commande `sudo gem install pg`.

## <a name="run-ruby-code"></a>Exécuter le code Ruby 
- Enregistrez le code dans un fichier texte avec l’extension .rb, puis enregistrez ce fichier dans un dossier du projet, par exemple `C:\rubypostgres\read.rb` ou `/home/username/rubypostgres/read.rb`.
- Pour exécuter le code, lancez l’invite de commandes ou l’interpréteur de commandes Bash. Remplacez le répertoire dans votre dossier de projet `cd rubypostgres`, puis saisissez la commande `ruby read.rb` pour exécuter l’application.

## <a name="get-connection-information"></a>Obtenir des informations de connexion
Obtenez les informations de connexion requises pour vous connecter à la base de données Azure pour PostgreSQL. Vous devez disposer du nom de serveur complet et des informations d’identification.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Dans le menu de gauche du portail Azure, cliquez sur **Toutes les ressources**, puis recherchez le serveur que vous venez de créer, par exemple **mydemoserver**.
3. Cliquez sur le nom du serveur.
4. Dans le panneau **Vue d’ensemble** du serveur, notez le **nom du serveur** et le **nom de connexion de l’administrateur du serveur**. Si vous oubliez votre mot de passe, vous pouvez également le réinitialiser dans ce panneau.
 ![Nom du serveur Azure Database pour PostgreSQL](./media/connect-ruby/1-connection-string.png)

## <a name="connect-and-create-a-table"></a>Se connecter et créer une table
Utilisez le code suivant pour vous connecter et créer une table à l’aide de l’instruction **CREATE TABLE**, suivie des instructions SQL **INSERT INTO** pour ajouter des lignes à la table.

Le code utilise un objet [PG::Connection](http://www.rubydoc.info/gems/pg/PG/Connection) objet avec le constructeur [new()](http://www.rubydoc.info/gems/pg/PG%2FConnection:initialize) pour se connecter à la base de données Azure pour PostgreSQL. Ensuite, il appelle la méthode [exec()](http://www.rubydoc.info/gems/pg/PG/Connection#exec-instance_method) pour exécuter les commandes DROP, CREATE TABLE et INSERT INTO. Le code vérifie les erreurs à l’aide de la classe [PG::Error](http://www.rubydoc.info/gems/pg/PG/Error). Ensuite, il appelle la méthode [close()](http://www.rubydoc.info/gems/pg/PG/Connection#lo_close-instance_method) pour fermer la connexion, avant de s’arrêter.

Remplacez les chaînes `host`, `database`, `user` et `password` par vos propres valeurs. 
```ruby
require 'pg'

begin
    # Initialize connection variables.
    host = String('mydemoserver.postgres.database.azure.com')
    database = String('postgres')
    user = String('mylogin@mydemoserver')
    password = String('<server_admin_password>')

    # Initialize connection object.
    connection = PG::Connection.new(:host => host, :user => user, :dbname => database, :port => '5432', :password => password)
    puts 'Successfully created connection to database'

    # Drop previous table of same name if one exists
    connection.exec('DROP TABLE IF EXISTS inventory;')
    puts 'Finished dropping table (if existed).'

    # Drop previous table of same name if one exists.
    connection.exec('CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);')
    puts 'Finished creating table.'

    # Insert some data into table.
    connection.exec("INSERT INTO inventory VALUES(1, 'banana', 150)")
    connection.exec("INSERT INTO inventory VALUES(2, 'orange', 154)")
    connection.exec("INSERT INTO inventory VALUES(3, 'apple', 100)")
    puts 'Inserted 3 rows of data.'

rescue PG::Error => e
    puts e.message 
    
ensure
    connection.close if connection
end
```

## <a name="read-data"></a>Lire les données
Utilisez le code suivant pour vous connecter et lire des données à l’aide d’une instruction SQL **SELECT**. 

Le code utilise un objet [PG::Connection](http://www.rubydoc.info/gems/pg/PG/Connection) objet avec le constructeur [new()](http://www.rubydoc.info/gems/pg/PG%2FConnection:initialize) pour se connecter à la base de données Azure pour PostgreSQL. Ensuite, il appelle la méthode [exec()](http://www.rubydoc.info/gems/pg/PG/Connection#exec-instance_method) pour exécuter la commande SELECT, en conservant les résultats dans un jeu de résultats. La collection de jeu de résultats est itérée au sein de la boucle `resultSet.each do`, les valeurs de ligne actuelles étant conservées dans la variable `row`. Le code vérifie les erreurs à l’aide de la classe [PG::Error](http://www.rubydoc.info/gems/pg/PG/Error). Ensuite, il appelle la méthode [close()](http://www.rubydoc.info/gems/pg/PG/Connection#lo_close-instance_method) pour fermer la connexion, avant de s’arrêter.

Remplacez les chaînes `host`, `database`, `user` et `password` par vos propres valeurs. 

```ruby
require 'pg'

begin
    # Initialize connection variables.
    host = String('mydemoserver.postgres.database.azure.com')
    database = String('postgres')
    user = String('mylogin@mydemoserver')
    password = String('<server_admin_password>')

    # Initialize connection object.
    connection = PG::Connection.new(:host => host, :user => user, :database => dbname, :port => '5432', :password => password)
    puts 'Successfully created connection to database.'

    resultSet = connection.exec('SELECT * from inventory;')
    resultSet.each do |row|
        puts 'Data row = (%s, %s, %s)' % [row['id'], row['name'], row['quantity']]
    end

rescue PG::Error => e
    puts e.message 
    
ensure
    connection.close if connection
end
```

## <a name="update-data"></a>Mettre à jour des données
Utilisez le code suivant pour vous connecter et mettre à jour les données à l’aide d’une instruction SQL **UPDATE**.

Le code utilise un objet [PG::Connection](http://www.rubydoc.info/gems/pg/PG/Connection) objet avec le constructeur [new()](http://www.rubydoc.info/gems/pg/PG%2FConnection:initialize) pour se connecter à la base de données Azure pour PostgreSQL. Ensuite, il appelle la méthode [exec()](http://www.rubydoc.info/gems/pg/PG/Connection#exec-instance_method) pour exécuter la commande UPDATE. Le code vérifie les erreurs à l’aide de la classe [PG::Error](http://www.rubydoc.info/gems/pg/PG/Error). Ensuite, il appelle la méthode [close()](http://www.rubydoc.info/gems/pg/PG/Connection#lo_close-instance_method) pour fermer la connexion, avant de s’arrêter.

Remplacez les chaînes `host`, `database`, `user` et `password` par vos propres valeurs. 

```ruby
require 'pg'

begin
    # Initialize connection variables.
    host = String('mydemoserver.postgres.database.azure.com')
    database = String('postgres')
    user = String('mylogin@mydemoserver')
    password = String('<server_admin_password>')

    # Initialize connection object.
    connection = PG::Connection.new(:host => host, :user => user, :dbname => database, :port => '5432', :password => password)
    puts 'Successfully created connection to database.'

    # Modify some data in table.
    connection.exec('UPDATE inventory SET quantity = %d WHERE name = %s;' % [200, '\'banana\''])
    puts 'Updated 1 row of data.'

rescue PG::Error => e
    puts e.message 
    
ensure
    connection.close if connection
end
```


## <a name="delete-data"></a>Suppression de données
Utilisez le code suivant pour vous connecter et lire des données à l’aide d’une instruction SQL **DELETE**. 

Le code utilise un objet [PG::Connection](http://www.rubydoc.info/gems/pg/PG/Connection) objet avec le constructeur [new()](http://www.rubydoc.info/gems/pg/PG%2FConnection:initialize) pour se connecter à la base de données Azure pour PostgreSQL. Ensuite, il appelle la méthode [exec()](http://www.rubydoc.info/gems/pg/PG/Connection#exec-instance_method) pour exécuter la commande UPDATE. Le code vérifie les erreurs à l’aide de la classe [PG::Error](http://www.rubydoc.info/gems/pg/PG/Error). Ensuite, il appelle la méthode [close()](http://www.rubydoc.info/gems/pg/PG/Connection#lo_close-instance_method) pour fermer la connexion, avant de s’arrêter.

Remplacez les chaînes `host`, `database`, `user` et `password` par vos propres valeurs. 

```ruby
require 'pg'

begin
    # Initialize connection variables.
    host = String('mydemoserver.postgres.database.azure.com')
    database = String('postgres')
    user = String('mylogin@mydemoserver')
    password = String('<server_admin_password>')

    # Initialize connection object.
    connection = PG::Connection.new(:host => host, :user => user, :dbname => database, :port => '5432', :password => password)
    puts 'Successfully created connection to database.'

    # Modify some data in table.
    connection.exec('DELETE FROM inventory WHERE name = %s;' % ['\'orange\''])
    puts 'Deleted 1 row of data.'

rescue PG::Error => e
    puts e.message 
    
ensure
    connection.close if connection
end
```

## <a name="next-steps"></a>étapes suivantes
> [!div class="nextstepaction"]
> [Migration de votre base de données PostgreSQL par exportation et importation](./howto-migrate-using-export-and-import.md)
