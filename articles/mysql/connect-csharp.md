---
title: "Se connecter à la base de données Azure pour MySQL avec C# | Microsoft Docs"
description: "Ce guide de démarrage rapide fournit un exemple de code C# (.NET) que vous pouvez utiliser pour vous connecter et interroger des données de la base de données Azure pour MySQL."
services: MySQL
author: seanli1988
ms.author: seal
manager: janders
editor: jasonwhowell
ms.service: MySQL
ms.custom: mvc
ms.devlang: csharp
ms.topic: quickstart
ms.date: 09/22/2017
ms.openlocfilehash: f87c3c302ec25f33af4334c3753dfae0084e4393
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-database-for-mysql-use-net-c-to-connect-and-query-data"></a>Base de données Azure pour MySQL : Utilisation de .NET (C#) pour vous connecter et interroger des données
Ce guide de démarrage rapide explique comment vous connecter à Azure Database pour MySQL en utilisant une application C#. Il détaille l’utilisation d’instructions SQL pour interroger la base de données, la mettre à jour, y insérer des données ou en supprimer. Cette rubrique part du principe que vous connaissez les bases du développement à l’aide de C# et que vous ne savez pas utiliser Azure Database pour MySQL.

## <a name="prerequisites"></a>Composants requis
Ce guide de démarrage rapide s’appuie sur les ressources créées dans l’un de ces guides :
- [Création d’un serveur Azure Database pour MySQL à l’aide du portail Azure](./quickstart-create-mysql-server-database-using-azure-portal.md)
- [Création d’un serveur de base de données Azure pour MySQL à l’aide d’Azure CLI](./quickstart-create-mysql-server-database-using-azure-cli.md)

Il vous faudra également :
- Installer [.NET](https://www.microsoft.com/net/download). Suivez les étapes de l’article associé pour installer .NET spécifiquement pour votre plateforme (Windows, Ubuntu Linux ou macOS). 
- Installer [Visual Studio](https://www.visualstudio.com/downloads/).
- Installer [le pilote ODBC pour MySQL](https://dev.mysql.com/downloads/connector/odbc/).

## <a name="get-connection-information"></a>Obtenir des informations de connexion
Obtenez les informations requises pour vous connecter à la base de données Azure pour MySQL. Vous devez disposer du nom de serveur complet et des informations d’identification.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Dans le menu de gauche du portail Azure, cliquez sur **Toutes les ressources**, puis recherchez le serveur que vous venez de créer, par exemple **myserver4demo**.
3. Cliquez sur le nom du serveur.
4. Sélectionnez la page **Propriétés** du serveur, puis notez le **nom du serveur** et le **nom de connexion de l’administrateur du serveur**.
 ![Nom du serveur de base de données Azure pour MySQL](./media/connect-csharp/1_server-properties-name-login.png)
5. Si vous avez oublié vos informations de connexion au serveur, accédez à la page **Vue d’ensemble** pour afficher le nom de connexion de l’administrateur du serveur et, si nécessaire, réinitialiser le mot de passe.

## <a name="connect-create-table-and-insert-data"></a>Se connecter, créer des tables et insérer des données
Utilisez le code suivant pour vous connecter et charger les données à l’aide d’instructions SQL **CREATE TABLE** et **INSERT INTO**. Le code utilise la classe ODBC avec la méthode [Open()](https://msdn.microsoft.com/library/system.data.odbc.odbcconnection.open(v=vs.110).aspx) pour établir une connexion à MySQL. Le code utilise ensuite la méthode [CreateCommand()](https://msdn.microsoft.com/library/system.data.odbc.odbcconnection.createcommand(v=vs.110).aspx), définit la propriété CommandText et appelle la méthode [ExecuteNonQuery()](https://msdn.microsoft.com/library/system.data.odbc.odbccommand.executenonquery(v=vs.110).aspx) pour exécuter les commandes de la base de données. 

Remplacez les paramètres Host, DBName, User et Password par les valeurs spécifiées lors de la création du serveur et de la base de données. 

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using MySql.Data;
using System.Data.Odbc;

namespace driver
{
    class MySQLCreate
    {
        static void Main(string[] args)
        {
            var conn = new OdbcConnection("DRIVER={MySQL ODBC 5.3 unicode Driver}; Server=myserver4demo.mysql.database.azure.com; Port=3306;" +
            " Database=quickstartdb; Uid=myadmin@myserver4demo; Pwd=server_admin_password; sslverify=0; Option=3;MULTI_STATEMENTS=1");

            Console.Out.WriteLine("Opening connection");
            conn.Open();

            var command = conn.CreateCommand();
            command.CommandText = "DROP TABLE IF EXISTS inventory;";
            command.ExecuteNonQuery();
            Console.Out.WriteLine("Finished dropping table (if existed)");

            command.CommandText = "CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);";
            command.ExecuteNonQuery();
            Console.Out.WriteLine("Finished creating table");

            command.CommandText =
                String.Format(
                    @"INSERT INTO inventory (name, quantity) VALUES ({0}, {1});
                    INSERT INTO inventory (name, quantity) VALUES ({2}, {3});
                    INSERT INTO inventory (name, quantity) VALUES ({4}, {5});",
                    "\'banana\'", 150,
                    "\'orange\'", 154,
                    "\'apple\'", 100
                    );

            int nRows = command.ExecuteNonQuery();
            Console.Out.WriteLine(String.Format("Number of rows inserted={0}", nRows));

            Console.Out.WriteLine("Closing connection");
            conn.Close();

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }

    }
}

```

## <a name="read-data"></a>Lire les données

Utilisez le code suivant pour vous connecter et lire des données à l’aide d’une instruction SQL **SELECT**. Le code utilise la classe ODBC avec la méthode [Open()](https://msdn.microsoft.com/library/system.data.odbc.odbcconnection.open(v=vs.110).aspx) pour établir une connexion à MySQL. Le code utilise ensuite la méthode [CreateCommand()](https://msdn.microsoft.com/library/system.data.odbc.odbcconnection.createcommand(v=vs.110).aspx) et la méthode [ExecuteReader()](https://msdn.microsoft.com/library/system.data.odbc.odbccommand.executereader(v=vs.110).aspx) pour exécuter les commandes de la base de données. Puis le code utilise [Read()](https://msdn.microsoft.com/library/system.data.odbc.odbcdatareader.read(v=vs.110).aspx) pour accéder à des enregistrements dans les résultats. Ensuite, le code utilise GetInt32 et GetString pour analyser les valeurs de l’enregistrement.

Remplacez les paramètres Host, DBName, User et Password par les valeurs spécifiées lors de la création du serveur et de la base de données. 

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using MySql.Data;
using System.Data.Odbc;


namespace driver
{
    class MySQLRead
    {

        static void Main(string[] args)
        {
            var conn = new OdbcConnection("DRIVER={MySQL ODBC 5.3 unicode Driver}; Server=myserver4demo.mysql.database.azure.com; Port=3306;" +
            " Database=quickstartdb; Uid=myadmin@myserver4demo; Pwd=server_admin_password; sslverify=0; Option=3;MULTI_STATEMENTS=1");

            Console.Out.WriteLine("Opening connection");
            conn.Open();

            var command = conn.CreateCommand();
            command.CommandText = "SELECT * FROM inventory;";

            var reader = command.ExecuteReader();
            while (reader.Read())
            {
                Console.WriteLine(
                    string.Format(
                        "Reading from table=({0}, {1}, {2})",
                        reader.GetInt32(0).ToString(),
                        reader.GetString(1),
                        reader.GetInt32(2).ToString()
                        )
                    );
            }

            Console.Out.WriteLine("Closing connection");
            conn.Close();

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }
    }
}


```

## <a name="update-data"></a>Mettre à jour des données
Utilisez le code suivant pour vous connecter et lire les données à l’aide d’une instruction SQL **UPDATE**. Le code utilise la classe ODBC avec la méthode [Open()](https://msdn.microsoft.com/library/system.data.odbc.odbcconnection.open(v=vs.110).aspx) pour établir une connexion à MySQL. Le code utilise ensuite la méthode [CreateCommand()](https://msdn.microsoft.com/library/system.data.odbc.odbcconnection.createcommand(v=vs.110).aspx), définit la propriété CommandText et appelle la méthode [ExecuteNonQuery()](https://msdn.microsoft.com/library/system.data.odbc.odbccommand.executenonquery(v=vs.110).aspx) pour exécuter les commandes de la base de données.

Remplacez les paramètres Host, DBName, User et Password par les valeurs spécifiées lors de la création du serveur et de la base de données. 

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Data.Odbc;

namespace driver
{
    class MySQLUpdate
    {
        static void Main(string[] args)
        {
            var conn = new OdbcConnection("DRIVER={MySQL ODBC 5.3 unicode Driver}; Server=myserver4demo.mysql.database.azure.com; Port=3306;" +
            " Database=quickstartdb; Uid=myadmin@myserver4demo; Pwd=server_admin_password; sslverify=0; Option=3;MULTI_STATEMENTS=1");

            Console.Out.WriteLine("Opening connection");
            conn.Open();

            var command = conn.CreateCommand();
            command.CommandText =
            String.Format("UPDATE inventory SET quantity = {0} WHERE name = {1};",
                200,
                "\'banana\'"
                );

            int nRows = command.ExecuteNonQuery();
            Console.Out.WriteLine(String.Format("Number of rows updated={0}", nRows));

            Console.Out.WriteLine("Closing connection");
            conn.Close();

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }
    }
}



```


## <a name="delete-data"></a>Suppression de données
Utilisez le code suivant pour vous connecter et supprimer les données à l’aide d’une instruction SQL **DELETE**. 

Le code utilise la classe ODBC avec la méthode [Open()](https://msdn.microsoft.com/library/system.data.odbc.odbcconnection.open(v=vs.110).aspx) pour établir une connexion à MySQL. Le code utilise ensuite la méthode [CreateCommand()](https://msdn.microsoft.com/library/system.data.odbc.odbcconnection.createcommand(v=vs.110).aspx), définit la propriété CommandText et appelle la méthode [ExecuteNonQuery()](https://msdn.microsoft.com/library/system.data.odbc.odbccommand.executenonquery(v=vs.110).aspx) pour exécuter les commandes de la base de données.

Remplacez les paramètres Host, DBName, User et Password par les valeurs spécifiées lors de la création du serveur et de la base de données. 

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Data.Odbc;

namespace driver
{
    class MySQLDelete
    {
        static void Main(string[] args)
        {
            var conn = new OdbcConnection("DRIVER={MySQL ODBC 5.3 unicode Driver}; Server=myserver4demo.mysql.database.azure.com; Port=3306;" +
            " Database=quickstartdb; Uid=myadmin@myserver4demo; Pwd=server_admin_password; sslverify=0; Option=3;MULTI_STATEMENTS=1");

            Console.Out.WriteLine("Opening connection");
            conn.Open();

            var command = conn.CreateCommand();
            command.CommandText =
                String.Format("DELETE FROM inventory WHERE name = {0};",
                    "\'orange\'");
            int nRows = command.ExecuteNonQuery();
            Console.Out.WriteLine(String.Format("Number of rows deleted={0}", nRows));

            Console.Out.WriteLine("Closing connection");
            conn.Close();

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }
    }
}

```

## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"]
> [Migrer une base de données MySQL vers une base de données Azure pour MySQL à l’aide des images mémoire et de la restauration](concepts-migrate-dump-restore.md)
