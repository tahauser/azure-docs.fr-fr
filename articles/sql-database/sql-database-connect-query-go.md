---
title: Utiliser Go pour interroger Azure SQL Database | Microsoft Docs
description: Utilisez Go pour créer un programme qui se connecte à une base de données SQL Azure, et utilisez des instructions Transact-SQL pour interroger et modifier des données.
services: sql-database
author: David-Engel
manager: craigg
ms.reviewer: MightyPen
ms.service: sql-database
ms.custom: mvc,develop apps
ms.devlang: go
ms.topic: quickstart
ms.date: 11/28/2017
ms.author: v-daveng
ms.openlocfilehash: ae13f6e3c530e2807d1f52e322663b65aebbb076
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="use-go-to-query-an-azure-sql-database"></a>Utiliser Go pour interroger une base de données Azure SQL

Ce didacticiel rapide montre comment utiliser [Go](https://godoc.org/github.com/denisenkom/go-mssqldb) pour se connecter à une base de données Azure SQL. Les instructions Transact-SQL permettant d’interroger et de modifier des données sont également présentées.

## <a name="prerequisites"></a>Prérequis


Pour suivre ce didacticiel de démarrage rapide, vérifiez que vous avez :

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

- Une [règle de pare-feu au niveau du serveur](sql-database-get-started-portal.md#create-a-server-level-firewall-rule) pour l’adresse IP publique de l’ordinateur que vous utilisez pour ce didacticiel de démarrage rapide.

- Installé Go et les logiciels connexes sur votre système d’exploitation :

    - **MacOS** : installez Homebrew et GoLang. Voir [l’Étape 1.2](https://www.microsoft.com/sql-server/developer-get-started/go/mac/).
    - **Ubuntu** : installez GoLang. Voir [l’Étape 1.2](https://www.microsoft.com/sql-server/developer-get-started/go/ubuntu/).
    - **Windows** : installez GoLang. Voir [l’Étape 1.2](https://www.microsoft.com/sql-server/developer-get-started/go/windows/).    

## <a name="sql-server-connection-information"></a>Informations de connexion SQL Server

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

## <a name="create-go-project-and-dependencies"></a>Créer un projet Go et des dépendances

1. À partir du terminal, créez un dossier de projet nommé **SqlServerSample**. 

   ```bash
   mkdir SqlServerSample
   ```

2. Accédez au répertoire **SqlServerSample**, puis obtenez et installez le pilote SQL Server pour Go :

   ```bash
   cd SqlServerSample
   go get github.com/denisenkom/go-mssqldb
   go install github.com/denisenkom/go-mssqldb
   ```

## <a name="create-sample-data"></a>Créer des exemples de données

1. À l’aide de l’éditeur de texte de votre choix, créez un fichier nommé **CreateTestData.sql** dans le dossier **SqlServerSample**. Copiez et collez le code T-SQL suivant dans ce fichier. Ce code crée un schéma et une table, puis insère quelques lignes.

   ```sql
   CREATE SCHEMA TestSchema;
   GO

   CREATE TABLE TestSchema.Employees (
     Id       INT IDENTITY(1,1) NOT NULL PRIMARY KEY,
     Name     NVARCHAR(50),
     Location NVARCHAR(50)
   );
   GO

   INSERT INTO TestSchema.Employees (Name, Location) VALUES
     (N'Jared',  N'Australia'),
     (N'Nikita', N'India'),
     (N'Tom',    N'Germany');
   GO

   SELECT * FROM TestSchema.Employees;
   GO
   ```

2. Connectez-vous à la base de données à l’aide de sqlcmd, puis exécutez le script SQL pour créer le schéma et la table et insérer des lignes. Remplacez les valeurs par celles correspondant à votre serveur, base de données, nom d’utilisateur et mot de passe.

   ```bash
   sqlcmd -S your_server.database.windows.net -U your_username -P your_password -d your_database -i ./CreateTestData.sql
   ```

## <a name="insert-code-to-query-sql-database"></a>Insertion du code pour interroger la base de données SQL

1. Créez un fichier nommé **sample.go** dans le dossier **SqlServerSample**.

2. Ouvrez le fichier et remplacez son contenu par le code suivant. Ajoutez les valeurs appropriées pour votre serveur, base de données, nom d’utilisateur et mot de passe. Cet exemple utilise les méthodes de contexte de GoLang pour garantir qu’il existe une connexion active au serveur de base de données.

   ```go
   package main

   import (
       _ "github.com/denisenkom/go-mssqldb"
       "database/sql"
       "context"
       "log"
       "fmt"
   )

   var db *sql.DB

   var server = "your_server.database.windows.net"
   var port = 1433
   var user = "your_username"
   var password = "your_password"
   var database = "your_database"

   func main() {
       // Build connection string
       connString := fmt.Sprintf("server=%s;user id=%s;password=%s;port=%d;database=%s;",
           server, user, password, port, database)

       var err error

       // Create connection pool
       db, err = sql.Open("sqlserver", connString)
       if err != nil {
           log.Fatal("Error creating connection pool:", err.Error())
       }
       fmt.Printf("Connected!\n")

       // Create employee
       createId, err := CreateEmployee("Jake", "United States")
       fmt.Printf("Inserted ID: %d successfully.\n", createId)

       // Read employees
       count, err := ReadEmployees()
       fmt.Printf("Read %d rows successfully.\n", count)

       // Update from database
       updateId, err := UpdateEmployee("Jake", "Poland")
       fmt.Printf("Updated row with ID: %d successfully.\n", updateId)

       // Delete from database
       rows, err := DeleteEmployee("Jake")
       fmt.Printf("Deleted %d rows successfully.\n", rows)
   }

   func CreateEmployee(name string, location string) (int64, error) {
       ctx := context.Background()
       var err error

       if db == nil {
           log.Fatal("What?")
       }

       // Check if database is alive.
       err = db.PingContext(ctx)
       if err != nil {
           log.Fatal("Error pinging database: " + err.Error())
       }

       tsql := fmt.Sprintf("INSERT INTO TestSchema.Employees (Name, Location) VALUES (@Name,@Location);")

       // Execute non-query with named parameters
       result, err := db.ExecContext(
           ctx,
           tsql,
           sql.Named("Location", location),
           sql.Named("Name", name))

       if err != nil {
           log.Fatal("Error inserting new row: " + err.Error())
           return -1, err
       }

       return result.LastInsertId()
   }

   func ReadEmployees() (int, error) {
       ctx := context.Background()

       // Check if database is alive.
       err := db.PingContext(ctx)
       if err != nil {
           log.Fatal("Error pinging database: " + err.Error())
       }

       tsql := fmt.Sprintf("SELECT Id, Name, Location FROM TestSchema.Employees;")

       // Execute query
       rows, err := db.QueryContext(ctx, tsql)
       if err != nil {
           log.Fatal("Error reading rows: " + err.Error())
           return -1, err
       }

       defer rows.Close()

       var count int = 0

       // Iterate through the result set.
       for rows.Next() {
           var name, location string
           var id int

           // Get values from row.
           err := rows.Scan(&id, &name, &location)
           if err != nil {
               log.Fatal("Error reading rows: " + err.Error())
               return -1, err
           }

           fmt.Printf("ID: %d, Name: %s, Location: %s\n", id, name, location)
           count++
       }

       return count, nil
   }

   // Update an employee's information
   func UpdateEmployee(name string, location string) (int64, error) {
       ctx := context.Background()

       // Check if database is alive.
       err := db.PingContext(ctx)
       if err != nil {
           log.Fatal("Error pinging database: " + err.Error())
       }

       tsql := fmt.Sprintf("UPDATE TestSchema.Employees SET Location = @Location WHERE Name= @Name")

       // Execute non-query with named parameters
       result, err := db.ExecContext(
           ctx,
           tsql,
           sql.Named("Location", location),
           sql.Named("Name", name))
       if err != nil {
           log.Fatal("Error updating row: " + err.Error())
           return -1, err
       }

       return result.LastInsertId()
   }

   // Delete an employee from database
   func DeleteEmployee(name string) (int64, error) {
       ctx := context.Background()

       // Check if database is alive.
       err := db.PingContext(ctx)
       if err != nil {
           log.Fatal("Error pinging database: " + err.Error())
       }

       tsql := fmt.Sprintf("DELETE FROM TestSchema.Employees WHERE Name=@Name;")

       // Execute non-query with named parameters
       result, err := db.ExecContext(ctx, tsql, sql.Named("Name", name))
       if err != nil {
           fmt.Println("Error deleting row: " + err.Error())
           return -1, err
       }

       return result.RowsAffected()
   }
   ```

## <a name="run-the-code"></a>Exécuter le code

1. Exécutez ensuite les commandes suivantes dans l’invite de commandes :

   ```bash
   go run sample.go
   ```

2. Vérifiez la sortie :

   ```text
   Connected!
   Inserted ID: 4 successfully.
   ID: 1, Name: Jared, Location: Australia
   ID: 2, Name: Nikita, Location: India
   ID: 3, Name: Tom, Location: Germany
   ID: 4, Name: Jake, Location: United States
   Read 4 rows successfully.
   Updated row with ID: 4 successfully.
   Deleted 1 rows successfully.
   ```

## <a name="next-steps"></a>Étapes suivantes

- [Concevoir votre première base de données SQL Azure](sql-database-design-first-database.md)
- [Pilote Go pour Microsoft SQL Server](https://github.com/denisenkom/go-mssqldb)
- [Signaler des problèmes ou poser des questions](https://github.com/denisenkom/go-mssqldb/issues)

