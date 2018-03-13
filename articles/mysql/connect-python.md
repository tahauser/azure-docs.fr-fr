---
title: "Se connecter à Azure Database pour MySQL à partir de Python"
description: "Ce guide de démarrage rapide fournit plusieurs exemples de code Python, que vous pouvez utiliser pour vous connecter et interroger des données d’Azure Database pour MySQL."
services: mysql
author: jasonwhowell
ms.author: jasonh
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.custom: mvc
ms.devlang: python
ms.topic: quickstart
ms.date: 02/28/2018
ms.openlocfilehash: 4003c1258fcc0a6d04c4ae8d234eba4d0224b8a0
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2018
---
# <a name="azure-database-for-mysql-use-python-to-connect-and-query-data"></a>Azure Database pour MySQL : Utiliser Python pour se connecter et interroger des données
Ce guide de démarrage rapide vous explique comment utiliser [Python](https://python.org) pour se connecter à une base de données Azure Database pour MySQL. Il utilise des instructions SQL pour interroger, insérer, mettre à jour et supprimer des données de la base de données sur des plateformes Mac OS et Ubuntu Linux et Windows. Cette rubrique part du principe que vous connaissez les bases du développement à l’aide de Python et que vous ne savez pas utiliser Azure Database pour MySQL.

## <a name="prerequisites"></a>configuration requise
Ce guide de démarrage rapide s’appuie sur les ressources créées dans l’un de ces guides :
- [Créer un serveur de base de données Azure pour MySQL à l’aide du Portail Azure](./quickstart-create-mysql-server-database-using-azure-portal.md)
- [Création d’un serveur de base de données Azure pour MySQL à l’aide d’Azure CLI](./quickstart-create-mysql-server-database-using-azure-cli.md)

## <a name="install-python-and-the-mysql-connector"></a>Installer Python et le connecteur MySQL
Installez [Python](https://www.python.org/downloads/) et le [connecteur MySQL pour Python](https://dev.mysql.com/downloads/connector/python/) sur votre ordinateur. Suivez les étapes correspondant à votre plateforme :

### <a name="windows"></a>Windows
1. Téléchargez et installez Python 2.7 sur [python.org](https://www.python.org/downloads/windows/). 
2. Vérifiez l’installation de Python en lançant l’invite de commandes. Exécutez la commande `C:\python27\python.exe -V` à l’aide du commutateur V majuscule pour voir le numéro de version.
3. Installez le connecteur Python pour MySQL correspondant à votre version de Python sur [mysql.com](https://dev.mysql.com/downloads/connector/python/).

### <a name="linux-ubuntu"></a>Linux (Ubuntu)
1. Sous Linux (Ubuntu), Python est généralement installé dans le cadre de l’installation par défaut.
2. Vérifiez l’installation de Python en lançant l’interpréteur de commandes Bash. Exécutez la commande `python -V` à l’aide du commutateur V majuscule pour voir le numéro de version.
3. Vérifier l’installation de PIP en exécutant la commande `pip show pip -V` pour voir le numéro de version. 
4. PIP peut être inclus dans certaines versions de Python. Si PIP n’est pas installé, vous pouvez installer le package [PIP] (https://pip.pypa.io/en/stable/installing/) en exécutant la commande `sudo apt-get install python-pip`.
5. Passez à la dernière version de PIP en exécutant la commande `pip install -U pip`.
6. Installez le connecteur MySQL pour Python et ses dépendances à l’aide de la commande PIP :

   ```bash
   sudo pip install mysql-connector-python-rf
   ```
 
### <a name="macos"></a>MacOS
1. Sous Mac OS, Python est généralement installé dans le cadre de l’installation par défaut du système d’exploitation.
2. Vérifiez l’installation de Python en lançant l’interpréteur de commandes Bash. Exécutez la commande `python -V` à l’aide du commutateur V majuscule pour voir le numéro de version.
3. Vérifier l’installation de PIP en exécutant la commande `pip show pip -V` pour voir le numéro de version.
4. PIP peut être inclus dans certaines versions de Python. Si PIP n’est pas installé, vous pouvez installer le package [PIP](https://pip.pypa.io/en/stable/installing/).
5. Passez à la dernière version de PIP en exécutant la commande `pip install -U pip`.
6. Installez le connecteur MySQL pour Python et ses dépendances à l’aide de la commande PIP :

   ```bash
   pip install mysql-connector-python-rf
   ```

## <a name="get-connection-information"></a>Obtenir des informations de connexion
Obtenez les informations requises pour vous connecter à la base de données Azure pour MySQL. Vous devez disposer du nom de serveur complet et des informations d’identification.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Dans le menu de gauche du portail Azure, cliquez sur **Toutes les ressources**, puis recherchez le serveur que vous venez de créer, par exemple **mydemoserver**.
3. Cliquez sur le nom du serveur.
4. Dans le panneau **Vue d’ensemble** du serveur, notez le **nom du serveur** et le **nom de connexion de l’administrateur du serveur**. Si vous oubliez votre mot de passe, vous pouvez également le réinitialiser dans ce panneau.
 ![Nom du serveur de base de données Azure pour MySQL](./media/connect-python/1_server-overview-name-login.png)
   

## <a name="run-python-code"></a>Exécuter du code Python
- Collez le code dans un fichier texte, puis enregistrez le fichier dans un dossier de projet avec l’extension de fichier .py (par exemple C:\pythonmysql\createtable.py ou /home/username/pythonmysql/createtable.py).
- Pour exécuter le code, lancez l’invite de commandes ou l’interpréteur de commandes Bash. Basculez dans votre dossier de projet : `cd pythonmysql`. Ensuite, tapez la commande Python suivie du nom de fichier pour exécuter l’application :`python createtable.py`. Sur le système d’exploitation Windows, si python.exe est introuvable, vous devrez peut-être fournir le chemin complet de l’exécutable ou ajouter le chemin Python à la variable d’environnement de chemin. `C:\python27\python.exe createtable.py`

## <a name="connect-create-table-and-insert-data"></a>Se connecter, créer des tables et insérer des données
Utilisez le code suivant pour vous connecter au serveur, créer une table et charger les données à l’aide d’une instruction SQL **INSERT**. 

Dans le code, la bibliothèque mysql.connector est importée. La fonction [connect()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) est utilisée pour se connecter à Azure Database pour MySQL à l’aide des [arguments de connexion](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) dans la collection de configurations. Le code utilise un curseur sur la connexion, et la méthode [cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) exécute la requête SQL sur la base de données MySQL. 

Remplacez les paramètres `host`, `user`, `password` et `database` par les valeurs que vous avez spécifiées lorsque vous avez créé le serveur et la base de données.

```Python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'mydemoserver.mysql.database.azure.com',
  'user':'myadmin@mydemoserver',
  'password':'yourpassword',
  'database':'quickstartdb'
}

# Construct connection string
try:
   conn = mysql.connector.connect(**config)
   print("Connection established")
except mysql.connector.Error as err:
  if err.errno == errorcode.ER_ACCESS_DENIED_ERROR:
    print("Something is wrong with the user name or password")
  elif err.errno == errorcode.ER_BAD_DB_ERROR:
    print("Database does not exist")
  else:
    print(err)
else:
  cursor = conn.cursor()

  # Drop previous table of same name if one exists
  cursor.execute("DROP TABLE IF EXISTS inventory;")
  print("Finished dropping table (if existed).")

  # Create table
  cursor.execute("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);")
  print("Finished creating table.")

  # Insert some data into table
  cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("banana", 150))
  print("Inserted",cursor.rowcount,"row(s) of data.")
  cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("orange", 154))
  print("Inserted",cursor.rowcount,"row(s) of data.")
  cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("apple", 100))
  print("Inserted",cursor.rowcount,"row(s) of data.")

  # Cleanup
  conn.commit()
  cursor.close()
  conn.close()
  print("Done.")
```

## <a name="read-data"></a>Lire les données
Utilisez le code suivant pour vous connecter et lire des données à l’aide d’une instruction SQL **SELECT**. 

Dans le code, la bibliothèque mysql.connector est importée. La fonction [connect()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) est utilisée pour se connecter à Azure Database pour MySQL à l’aide des [arguments de connexion](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) dans la collection de configurations. Le code utilise un curseur sur la connexion, et la méthode [cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) exécute l’instruction SQL sur la base de données MySQL. Les lignes de données sont lues à l’aide de la méthode [fetchall()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-fetchall.html). Le jeu de résultats est conservé dans une ligne de la collection et un itérateur for est utilisé pour exécuter les lignes en boucle.

Remplacez les paramètres `host`, `user`, `password` et `database` par les valeurs que vous avez spécifiées lorsque vous avez créé le serveur et la base de données.

```Python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'mydemoserver.mysql.database.azure.com',
  'user':'myadmin@mydemoserver',
  'password':'yourpassword',
  'database':'quickstartdb'
}

# Construct connection string
try:
   conn = mysql.connector.connect(**config)
   print("Connection established")
except mysql.connector.Error as err:
  if err.errno == errorcode.ER_ACCESS_DENIED_ERROR:
    print("Something is wrong with the user name or password")
  elif err.errno == errorcode.ER_BAD_DB_ERROR:
    print("Database does not exist")
  else:
    print(err)
else:
  cursor = conn.cursor()

  # Read data
  cursor.execute("SELECT * FROM inventory;")
  rows = cursor.fetchall()
  print("Read",cursor.rowcount,"row(s) of data.")

  # Print all rows
  for row in rows:
    print("Data row = (%s, %s, %s)" %(str(row[0]), str(row[1]), str(row[2])))

  # Cleanup
  conn.commit()
  cursor.close()
  conn.close()
  print("Done.")
```

## <a name="update-data"></a>Mettre à jour des données
Utilisez le code suivant pour vous connecter et mettre à jour les données à l’aide d’une instruction SQL **UPDATE**. 

Dans le code, la bibliothèque mysql.connector est importée.  La fonction [connect()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) est utilisée pour se connecter à Azure Database pour MySQL à l’aide des [arguments de connexion](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) dans la collection de configurations. Le code utilise un curseur sur la connexion, et la méthode [cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) exécute l’instruction SQL sur la base de données MySQL. 

Remplacez les paramètres `host`, `user`, `password` et `database` par les valeurs que vous avez spécifiées lorsque vous avez créé le serveur et la base de données.

```Python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'mydemoserver.mysql.database.azure.com',
  'user':'myadmin@mydemoserver',
  'password':'yourpassword',
  'database':'quickstartdb'
}

# Construct connection string
try:
   conn = mysql.connector.connect(**config)
   print("Connection established")
except mysql.connector.Error as err:
  if err.errno == errorcode.ER_ACCESS_DENIED_ERROR:
    print("Something is wrong with the user name or password")
  elif err.errno == errorcode.ER_BAD_DB_ERROR:
    print("Database does not exist")
  else:
    print(err)
else:
  cursor = conn.cursor()

  # Update a data row in the table
  cursor.execute("UPDATE inventory SET quantity = %s WHERE name = %s;", (200, "banana"))
  print("Updated",cursor.rowcount,"row(s) of data.")

  # Cleanup
  conn.commit()
  cursor.close()
  conn.close()
  print("Done.")
```

## <a name="delete-data"></a>Suppression de données
Utilisez le code suivant pour vous connecter et supprimer des données à l’aide d’une instruction SQL **DELETE**. 

Dans le code, la bibliothèque mysql.connector est importée.  La fonction [connect()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) est utilisée pour se connecter à Azure Database pour MySQL à l’aide des [arguments de connexion](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) dans la collection de configurations. Le code utilise un curseur sur la connexion, et la méthode [cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) exécute la requête SQL sur la base de données MySQL. 

Remplacez les paramètres `host`, `user`, `password` et `database` par les valeurs que vous avez spécifiées lorsque vous avez créé le serveur et la base de données.

```Python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'mydemoserver.mysql.database.azure.com',
  'user':'myadmin@mydemoserver',
  'password':'yourpassword',
  'database':'quickstartdb'
}

# Construct connection string
try:
   conn = mysql.connector.connect(**config)
   print("Connection established.")
except mysql.connector.Error as err:
  if err.errno == errorcode.ER_ACCESS_DENIED_ERROR:
    print("Something is wrong with the user name or password.")
  elif err.errno == errorcode.ER_BAD_DB_ERROR:
    print("Database does not exist.")
  else:
    print(err)
else:
  cursor = conn.cursor()

  # Delete a data row in the table
  cursor.execute("DELETE FROM inventory WHERE name=%(param1)s;", {'param1':"orange"})
  print("Deleted",cursor.rowcount,"row(s) of data.")

  # Cleanup
  conn.commit()
  cursor.close()
  conn.close()
  print("Done.")
```

## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"]
> [Migration de votre base de données PostgreSQL par exportation et importation](./concepts-migrate-import-export.md)
