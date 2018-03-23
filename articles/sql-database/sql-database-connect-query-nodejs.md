---
title: Utilisation de Node.js pour interroger Azure SQL Database | Microsoft Docs
description: Cette rubrique vous explique comment utiliser Node.js pour créer un programme qui se connecte à une base de données SQL Azure et l’interroger à l’aide d’instructions Transact-SQL.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: mvc,develop apps
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 07/06/2017
ms.author: carlrab
ms.openlocfilehash: 3f28f360eda52087c8fca841bbd0a6804185813f
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="use-nodejs-to-query-an-azure-sql-database"></a>Utilisation de Node.js pour interroger une base de données SQL Azure

Ce didacticiel de démarrage rapide montre comment utiliser [Node.js](https://nodejs.org/en/) pour créer un programme qui se connecte à une base de données SQL Azure et utiliser des instructions Transact-SQL pour interroger des données.

## <a name="prerequisites"></a>Prérequis


Pour suivre ce didacticiel de démarrage rapide, vérifiez que vous avez :

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

- Une [règle de pare-feu au niveau du serveur](sql-database-get-started-portal.md#create-a-server-level-firewall-rule) pour l’adresse IP publique de l’ordinateur que vous utilisez pour ce didacticiel de démarrage rapide.

- Installé Node.js et les logiciels connexes pour votre système d’exploitation :
    - **MacOS** : installez Homebrew et Node.js, puis installez le pilote ODBC et SQLCMD. Consultez les [étapes 1.2 et 1.3](https://www.microsoft.com/sql-server/developer-get-started/node/mac/).
    - **Ubuntu** : installez Node.js, puis installez le pilote ODBC et SQLCMD. Consultez les [étapes 1.2 et 1.3](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu/).
    - **Windows** : installez Chocolatey et Node.js, puis installez le pilote ODBC et SQLCMD. Consultez les [étapes 1.2 et 1.3](https://www.microsoft.com/sql-server/developer-get-started/node/windows/).

## <a name="sql-server-connection-information"></a>Informations de connexion SQL Server

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

> [!IMPORTANT]
> Une règle de pare-feu doit être en place pour l’adresse IP publique de l’ordinateur sur lequel vous effectuez ce didacticiel. Si vous êtes sur un autre ordinateur ou si vous avez une autre adresse IP publique, créez une [règle de pare-feu au niveau du serveur à l’aide du portail Azure](sql-database-get-started-portal.md#create-a-server-level-firewall-rule). 

## <a name="create-a-nodejs-project"></a>Création d’un projet Node.js

Ouvrez une invite de commandes et créez un dossier nommé *sqltest*. Accédez au dossier que vous avez créé et exécutez la commande suivante :

    
    npm init -y
    npm install tedious
    npm install async
    

## <a name="insert-code-to-query-sql-database"></a>Insertion du code pour interroger la base de données SQL

1. Dans votre environnement de développement ou votre éditeur de texte favori, créez un nouveau fichier nommé **sqltest.js**.

2. Remplacez le contenu par le code suivant et ajoutez les valeurs appropriées pour votre serveur, base de données, utilisateur et mot de passe.

   ```js
   var Connection = require('tedious').Connection;
   var Request = require('tedious').Request;

   // Create connection to database
   var config = 
      {
        userName: 'someuser', // update me
        password: 'somepassword', // update me
        server: 'edmacasqlserver.database.windows.net', // update me
        options: 
           {
              database: 'somedb' //update me
              , encrypt: true
           }
      }
   var connection = new Connection(config);

   // Attempt to connect and execute queries if connection goes through
   connection.on('connect', function(err) 
      {
        if (err) 
          {
             console.log(err)
          }
       else
          {
              queryDatabase()
          }
      }
    );

   function queryDatabase()
      { console.log('Reading rows from the Table...');

          // Read all rows from table
        request = new Request(
             "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid",
                function(err, rowCount, rows) 
                   {
                       console.log(rowCount + ' row(s) returned');
                       process.exit();
                   }
               );
    
        request.on('row', function(columns) {
           columns.forEach(function(column) {
               console.log("%s\t%s", column.metadata.colName, column.value);
            });
                });
        connection.execSql(request);
      }
```

## <a name="run-the-code"></a>Exécuter le code

1. Exécutez ensuite les commandes suivantes dans l’invite de commandes :

   ```js
   node sqltest.js
   ```

2. Vérifiez que les 20 premières lignes sont renvoyées, puis fermez la fenêtre d’application.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur le [pilote Microsoft Node.js pour SQL Server](https://docs.microsoft.com/sql/connect/node-js/node-js-driver-for-sql-server/)
- Découvrez comment [connecter et interroger une base de données SQL Azure à l’aide de .NET Core](sql-database-connect-query-dotnet-core.md) sur Windows/Linux/macOS.  
- En savoir plus sur la [prise en main de .NET Core sur Windows/Linux/macOS à l’aide de la ligne de commande](/dotnet/core/tutorials/using-with-xplat-cli).
- Découvrez comment [concevoir votre première base de données SQL Azure à l’aide de SSMS](sql-database-design-first-database.md) ou [concevoir votre première base de données SQL Azure à l’aide de .NET](sql-database-design-first-database-csharp.md).
- Découvrez comment [se connecter et effectuer des requêtes avec SSMS](sql-database-connect-query-ssms.md)
- Découvrez comment [se connecter et effectuer des requêtes avec Visual Studio Code](sql-database-connect-query-vscode.md).

