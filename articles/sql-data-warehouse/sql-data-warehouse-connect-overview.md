---
title: "Se connecter à Azure SQL Data Warehouse | Microsoft Docs"
description: "Comment trouver le nom de serveur et la chaîne de connexion pour Azure SQL Data Warehouse"
services: sql-data-warehouse
documentationcenter: NA
author: antvgski
manager: jhubbard
editor: 
ms.assetid: e52872ca-ae74-4e25-9c56-d49c85c8d0f0
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: connect
ms.date: 12/14/2017
ms.author: anvang;barbkess;kevin
ms.openlocfilehash: 804d91d309fcce42f0ac50ac98dbad9c5648b4eb
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
# <a name="connect-to-azure-sql-data-warehouse"></a>Se connecter à Azure SQL Data Warehouse
Cet article vous aidera à établir votre première connexion à SQL Data Warehouse.

## <a name="find-your-server-name"></a>Recherche du nom de serveur
La première étape de connexion à SQL Data Warehouse consiste à déterminer le nom de votre serveur.  Dans l’exemple suivant, le nom du serveur est samplesvr.database.windows.net. Pour rechercher le nom complet du serveur :

1. Accédez au [portail Azure][Azure portal].
2. Cliquez sur **Entrepôts de données SQL**.
3. Cliquez sur l’entrepôt de données auquel vous souhaitez vous connecter.
4. Recherchez le nom complet du serveur.
   
    ![Nom complet du serveur][1]

## <a name="supported-drivers-and-connection-strings"></a>Chaînes de connexion et pilotes pris en charge
Azure SQL Data Warehouse prend en charge [ADO.NET][ADO.NET], [ODBC][ODBC], [PHP][PHP] et [JDBC][JDBC]. Cliquez sur l’un des pilotes ci-dessus pour rechercher la dernière version et accéder à la documentation connexe. Pour générer automatiquement la chaîne de connexion pour le pilote que vous utilisez à partir du portail Azure, vous pouvez cliquer sur l’option **Afficher les chaînes de connexion de la base de données** dans l’exemple précédent.  Voici également quelques exemples montrant à quoi ressemble une chaîne de connexion pour chaque pilote.

> [!NOTE]
> Vous pouvez définir le délai de connexion à 300 secondes pour permettre à votre connexion de résister à des courtes périodes d’indisponibilité.
> 
> 

### <a name="adonet-connection-string-example"></a>Exemple de chaîne de connexion ADO.NET
```csharp
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

### <a name="odbc-connection-string-example"></a>Exemple de chaîne de connexion ODBC
```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

### <a name="php-connection-string-example"></a>Exemple de chaîne de connexion PHP
```PHP
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.database.windows.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

### <a name="jdbc-connection-string-example"></a>Exemple de chaîne de connexion JDBC
```Java
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

## <a name="connection-settings"></a>Paramètres de connexion
SQL Data Warehouse standardise certains paramètres pendant la connexion et la création d’objets. Ces paramètres, qui ne peuvent pas être remplacés, comprennent notamment :

| Paramètre de base de données | Valeur |
|:--- |:--- |
| [ANSI_NULLS][ANSI_NULLS] |ACTIVÉ |
| [QUOTED_IDENTIFIERS][QUOTED_IDENTIFIERS] |ACTIVÉ |
| [DATEFORMAT][DATEFORMAT] |mdy |
| [DATEFIRST][DATEFIRST] |7 |

## <a name="next-steps"></a>Étapes suivantes
Pour vous connecter à Visual Studio et l’utiliser pour lancer des requêtes, consultez la page [Envoyer des requêtes avec Visual Studio][Query with Visual Studio]. Pour en savoir plus sur les options d’authentification, consultez la page [Authentification sur Azure SQL Data Warehouse][Authentication to Azure SQL Data Warehouse].

<!--Articles-->
[Query with Visual Studio]: ./sql-data-warehouse-query-visual-studio.md
[Authentication to Azure SQL Data Warehouse]: ./sql-data-warehouse-authentication.md

<!--MSDN references-->
[ADO.NET]: https://msdn.microsoft.com/library/e80y5yhx(v=vs.110).aspx
[ODBC]: https://msdn.microsoft.com/library/jj730314.aspx
[PHP]: https://msdn.microsoft.com/library/cc296172.aspx?f=255&MSPPError=-2147217396
[JDBC]: https://msdn.microsoft.com/library/mt484311(v=sql.110).aspx
[ANSI_NULLS]: https://msdn.microsoft.com/library/ms188048.aspx
[QUOTED_IDENTIFIERS]: https://msdn.microsoft.com/library/ms174393.aspx
[DATEFORMAT]: https://msdn.microsoft.com/library/ms189491.aspx
[DATEFIRST]: https://msdn.microsoft.com/library/ms181598.aspx

<!--Other-->
[Azure portal]: https://portal.azure.com

<!--Image references-->
[1]: media/sql-data-warehouse-connect-overview/server-connect.PNG


