---
title: "Se connecter à Azure Database pour PostgreSQL à partir de Python"
description: "Ce guide de démarrage rapide fournit un exemple de code Python que vous pouvez utiliser pour vous connecter et interroger des données de la base de données Azure pour PostgreSQL."
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.custom: mvc, devcenter
ms.devlang: python
ms.topic: quickstart
ms.date: 02/28/2018
ms.openlocfilehash: 926b35f49169c5a87e82996ba714aad40ba5244c
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2018
---
# <a name="azure-database-for-postgresql-use-python-to-connect-and-query-data"></a>Base de données Azure pour PostgreSQL : Utilisation de Python pour se connecter et interroger des données
Ce guide de démarrage rapide vous explique comment utiliser [Python](https://python.org) pour se connecter à une base de données Azure pour PostgreSQL. Il explique aussi comment utiliser des instructions SQL pour interroger, insérer, mettre à jour et supprimer des données de la base de données sur des plateformes Mac OS, Ubuntu Linux et Windows. Cet article suppose que vous connaissez les bases du développement via Python, et que vous ne savez pas utiliser la base de données Azure pour PostgreSQL.

## <a name="prerequisites"></a>configuration requise
Ce guide de démarrage rapide s’appuie sur les ressources créées dans l’un de ces guides :
- [Créer une base de données - Portail](quickstart-create-server-database-portal.md)
- [Créer une base de données - CLI](quickstart-create-server-database-azure-cli.md)

Vous aurez également besoin des éléments suivants :
- Installation de [python](https://www.python.org/downloads/)
- Installation du package [pip](https://pip.pypa.io/en/stable/installing/) (déjà installé si vous utilisez des binaires Python 2 >=2.7.9 ou Python 3 >=3.4 téléchargés depuis [python.org](https://python.org)).

## <a name="install-the-python-connection-libraries-for-postgresql"></a>Installer des bibliothèques de connexions Python pour PostgreSQL
Installez le package [psycopg2](http://initd.org/psycopg/docs/install.html), qui vous permet de vous connecter et d’interroger la base de données. Il est [disponible sur PyPI](https://pypi.python.org/pypi/psycopg2/) sous la forme de package en [roue](http://pythonwheels.com/) et pour les plateformes courantes (Linux, OSX, Windows). Utilisez l’installation de pip pour obtenir la version binaire du module qui inclut toutes les dépendances.

1. Sur votre ordinateur, lancez une interface de ligne de commande :
    - Sur Linux, lancez l’interpréteur de commandes Bash.
    - Sur macOS, lancez le Terminal.
    - Sur Windows, lancez l’invite de commandes à partir du menu Démarrer.
2. Veillez à utiliser la version la plus récente de pip en exécutant cette commande, par exemple :
    ```cmd
    pip install -U pip
    ```

3. Exécutez la commande suivante pour installer le package psycopg2 :
    ```cmd
    pip install psycopg2
    ```

## <a name="get-connection-information"></a>Obtenir des informations de connexion
Obtenez les informations de connexion requises pour vous connecter à la base de données Azure pour PostgreSQL. Vous devez disposer du nom de serveur complet et des informations d’identification.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Dans le menu de gauche du portail Azure, cliquez sur **Toutes les ressources**, puis recherchez le serveur que vous venez de créer, par exemple **mydemoserver**.
3. Cliquez sur le nom du serveur.
4. Dans le panneau **Vue d’ensemble** du serveur, notez le **nom du serveur** et le **nom de connexion de l’administrateur du serveur**. Si vous oubliez votre mot de passe, vous pouvez également le réinitialiser dans ce panneau.
 ![Nom du serveur Azure Database pour PostgreSQL](./media/connect-python/1-connection-string.png)

## <a name="how-to-run-python-code"></a>Comment exécuter du code Python
Cet article contient un total de quatre exemples de code qui assurent chacun une fonction spécifique. Les instructions suivantes expliquent comment créer un fichier texte, insérer un bloc de code, et enregistrer ce fichier pour pouvoir l’exécuter ultérieurement. Veillez à créer quatre fichiers différents, un pour chaque bloc de code.

- Dans l’éditeur de texte que vous préférez, créez un nouveau fichier.
- Dans le fichier texte, copiez et collez un des exemples de code présents dans les sections suivantes. Remplacez les paramètres **host**, **dbname**, **user** et **password** par les valeurs spécifiées lors de la création du serveur et de la base de données.
- Enregistrez le fichier avec l’extension .py (par exemple postgres.py) dans votre dossier de projet. Si vous utilisez Windows, veillez à sélectionner l’encodage UTF-8 lors de l’enregistrement du fichier. 
- Lancez l’invite de commande, le terminal ou l’interpréteur de commandes Bash, puis modifiez le répertoire de votre dossier de projet, par exemple `cd postgres`.
-  Pour exécuter le code, tapez la commande Python suivie du nom de fichier, par exemple `Python postgres.py`.

> [!NOTE]
> À partir de Python version 3, vous pouvez rencontrer l’erreur `SyntaxError: Missing parentheses in call to 'print'` lorsque vous exécutez les blocs de code suivants ; dans ce cas, remplacez chaque appel à la commande `print "string"` par un appel de fonction en utilisant des parenthèses, comme `print("string")`.

## <a name="connect-create-table-and-insert-data"></a>Se connecter, créer des tables et insérer des données
Utilisez le code suivant pour vous connecter et charger les données à l’aide de la fonction [psycopg2.connect](http://initd.org/psycopg/docs/connection.html), avec l’instruction SQL **INSERT**. La fonction [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) est utilisée pour exécuter la requête SQL sur la base de données PostgreSQL. Remplacez les paramètres host, dbname, user et password par les valeurs spécifiées lors de la création du serveur et de la base de données.

```Python
import psycopg2

# Update connection string information obtained from the portal
host = "mydemoserver.postgres.database.azure.com"
user = "mylogin@mydemoserver"
dbname = "mypgsqldb"
password = "<server_admin_password>"
sslmode = "require"

# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3} sslmode={4}".format(host, user, dbname, password, sslmode)
conn = psycopg2.connect(conn_string) 
print "Connection established"

cursor = conn.cursor()

# Drop previous table of same name if one exists
cursor.execute("DROP TABLE IF EXISTS inventory;")
print "Finished dropping table (if existed)"

# Create table
cursor.execute("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);")
print "Finished creating table"

# Insert some data into table
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("banana", 150))
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("orange", 154))
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("apple", 100))
print "Inserted 3 rows of data"

# Cleanup
conn.commit()
cursor.close()
conn.close()
```

Une fois que le code s’exécute avec succès, la sortie s’affiche comme suit :

![Sortie de la ligne de commande](media/connect-python/2-example-python-output.png)

## <a name="read-data"></a>Lire les données
Utilisez le code suivant pour lire les données insérées à l’aide de la fonction [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute), avec l’instruction SQL **SELECT**. Cette fonction accepte une requête et renvoie un jeu de résultats qui peut être itéré à l’aide de [cursor.fetchall()](http://initd.org/psycopg/docs/cursor.html#cursor.fetchall). Remplacez les paramètres host, dbname, user et password par les valeurs spécifiées lors de la création du serveur et de la base de données.

```Python
import psycopg2

# Update connection string information obtained from the portal
host = "mydemoserver.postgres.database.azure.com"
user = "mylogin@mydemoserver"
dbname = "mypgsqldb"
password = "<server_admin_password>"
sslmode = "require"

# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3} sslmode={4}".format(host, user, dbname, password, sslmode)
conn = psycopg2.connect(conn_string) 
print "Connection established"

cursor = conn.cursor()

# Fetch all rows from table
cursor.execute("SELECT * FROM inventory;")
rows = cursor.fetchall()

# Print all rows
for row in rows:
    print "Data row = (%s, %s, %s)" %(str(row[0]), str(row[1]), str(row[2]))

# Cleanup
conn.commit()
cursor.close()
conn.close()
```

## <a name="update-data"></a>Mettre à jour des données
Utilisez le code suivant pour mettre à jour la ligne d’inventaire que vous avez insérée précédemment à l’aide de la fonction [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) et de l’instruction SQL **UPDATE**. Remplacez les paramètres host, dbname, user et password par les valeurs spécifiées lors de la création du serveur et de la base de données.

```Python
import psycopg2

# Update connection string information obtained from the portal
host = "mydemoserver.postgres.database.azure.com"
user = "mylogin@mydemoserver"
dbname = "mypgsqldb"
password = "<server_admin_password>"
sslmode = "require"

# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3} sslmode={4}".format(host, user, dbname, password, sslmode)
conn = psycopg2.connect(conn_string) 
print "Connection established"

cursor = conn.cursor()

# Update a data row in the table
cursor.execute("UPDATE inventory SET quantity = %s WHERE name = %s;", (200, "banana"))
print "Updated 1 row of data"

# Cleanup
conn.commit()
cursor.close()
conn.close()
```

## <a name="delete-data"></a>Suppression de données
Utilisez le code suivant pour supprimer une ligne d’inventaire que vous avez insérée précédemment à l’aide de la fonction [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) et de l’instruction SQL **DELETE**. Remplacez les paramètres host, dbname, user et password par les valeurs spécifiées lors de la création du serveur et de la base de données.

```Python
import psycopg2

# Update connection string information obtained from the portal
host = "mydemoserver.postgres.database.azure.com"
user = "mylogin@mydemoserver"
dbname = "mypgsqldb"
password = "<server_admin_password>"
sslmode = "require"

# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3} sslmode={4}".format(host, user, dbname, password, sslmode)
conn = psycopg2.connect(conn_string) 
print "Connection established"

cursor = conn.cursor()

# Delete data row from table
cursor.execute("DELETE FROM inventory WHERE name = %s;", ("orange",))
print "Deleted 1 row of data"

# Cleanup
conn.commit()
cursor.close()
conn.close()
```

## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"]
> [Migration de votre base de données PostgreSQL par exportation et importation](./howto-migrate-using-export-and-import.md)
