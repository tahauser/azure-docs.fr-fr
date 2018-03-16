---
title: "Utilisation de PHP pour interroger Azure SQL Database | Microsoft Docs"
description: "Cette rubrique vous explique comment utiliser PHP pour créer un programme qui se connecte à une base de données SQL Azure et l’interroger à l’aide d’instructions Transact-SQL."
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 4e71db4a-a 22f-4f1c-83e5-4a34a036ecf3
ms.service: sql-database
ms.custom: mvc,develop apps
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: php
ms.topic: quickstart
ms.date: 11/29/2017
ms.author: carlrab
ms.openlocfilehash: a562851330d325ca4a8be20ec82b5f421e23c4f1
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="use-php-to-query-an-azure-sql-database"></a>Utilisation de PHP pour interroger une base de données SQL Azure

Ce didacticiel de démarrage rapide montre comment utiliser [PHP](http://php.net/manual/en/intro-whatis.php) pour créer un programme qui se connecte à une base de données SQL Azure et utiliser des instructions Transact-SQL pour interroger des données.

## <a name="prerequisites"></a>Prérequis


Pour suivre ce didacticiel de démarrage rapide, vérifiez que vous avez :

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

- Une [règle de pare-feu au niveau du serveur](sql-database-get-started-portal.md#create-a-server-level-firewall-rule) pour l’adresse IP publique de l’ordinateur que vous utilisez pour ce didacticiel de démarrage rapide.

- Installé PHP et les logiciels connexes sur votre système d’exploitation :

    - **MacOS** : installez Homebrew et PHP, installez le pilote ODBC et SQLCMD, puis installez le pilote PHP pour SQL Server. Consultez les [étapes 1.2, 1.3 et 2.1](https://www.microsoft.com/sql-server/developer-get-started/php/mac/).
    - **Ubuntu** : installez PHP et les autres packages requis, puis installez le pilote PHP pour SQL Server. Consultez les [étapes 1.2 et 2.1](https://www.microsoft.com/sql-server/developer-get-started/php/ubuntu/).
    - **Windows**: installez la version la plus récente de PHP pour IIS Express, la version la plus récente des Pilotes Microsoft SQL Server dans IIS Express, Chocolatey, le pilote ODBC et SQLCMD. Consultez les [étapes 1.2 et 1.3](https://www.microsoft.com/sql-server/developer-get-started/php/windows/).    

## <a name="sql-server-connection-information"></a>Informations de connexion SQL Server

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]
    
## <a name="insert-code-to-query-sql-database"></a>Insertion du code pour interroger la base de données SQL

1. Dans votre éditeur de texte favori, créez un nouveau fichier nommé **sqltest.php**.  

2. Remplacez le contenu par le code suivant et ajoutez les valeurs appropriées pour votre serveur, base de données, utilisateur et mot de passe.

   ```PHP
   <?php
   $serverName = "your_server.database.windows.net";
   $connectionOptions = array(
       "Database" => "your_database",
       "Uid" => "your_username",
       "PWD" => "your_password"
   );
   //Establishes the connection
   $conn = sqlsrv_connect($serverName, $connectionOptions);
   $tsql= "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
           FROM [SalesLT].[ProductCategory] pc
           JOIN [SalesLT].[Product] p
        ON pc.productcategoryid = p.productcategoryid";
   $getResults= sqlsrv_query($conn, $tsql);
   echo ("Reading data from table" . PHP_EOL);
   if ($getResults == FALSE)
       echo (sqlsrv_errors());
   while ($row = sqlsrv_fetch_array($getResults, SQLSRV_FETCH_ASSOC)) {
    echo ($row['CategoryName'] . " " . $row['ProductName'] . PHP_EOL);
   }
   sqlsrv_free_stmt($getResults);
   ?>
   ```

## <a name="run-the-code"></a>Exécuter le code

1. Exécutez ensuite les commandes suivantes dans l’invite de commandes :

   ```php
   php sqltest.php
   ```

2. Vérifiez que les 20 premières lignes sont renvoyées, puis fermez la fenêtre d’application.

## <a name="next-steps"></a>Étapes suivantes
- [Concevoir votre première base de données SQL Azure](sql-database-design-first-database.md)
- [Microsoft PHP Drivers for SQL Server](https://github.com/Microsoft/msphpsql/) (Pilotes PHP Microsoft pour SQL Server)
- [Signaler des problèmes ou poser des questions](https://github.com/Microsoft/msphpsql/issues)
- [Exemple de logique de nouvelle tentative : Connexion résiliente à SQL avec PHP][step-4-connect-resiliently-to-sql-with-php-p42h]


<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-php-p42h]: https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php

