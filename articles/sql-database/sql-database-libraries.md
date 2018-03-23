---
title: Bibliothèques de connexions pour SQL Database | Microsoft Docs
description: Fournit des liens de téléchargement de modules qui permettent de se connecter à SQL Server et SQL Database à partir d’un grand nombre de langages de programmation côté client.
services: sql-database
author: MightyPen
manager: craigg
ms.service: sql-database
ms.custom: develop apps
ms.topic: article
ms.date: 11/29/2017
ms.author: genemi
ms.openlocfilehash: f371afc89273af4ce1c6c55eccc99a6f8a6349d5
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="connectivity-libraries-and-frameworks-for-sql-server"></a>Bibliothèques et frameworks de connectivité pour SQL Server

Découvrez nos [didacticiels de démarrage](http://aka.ms/sqldev) pour commencer rapidement à utiliser des langages de programmation comme C#, Java, Node.js, PHP et Python. Ensuite, créez une application à l’aide de SQL Server sur Linux, Windows ou Docker sur macOS.

Le tableau suivant répertorie les bibliothèques de connectivité ou *pilotes* que les applications clientes peuvent utiliser dans une variété de langages pour se connecter à SQL Server et l’utiliser localement ou dans le cloud. Vous pouvez les utiliser sur Linux, Windows ou Docker, pour vous connecter à Azure SQL Database et Azure SQL Data Warehouse. 

| Langage | Plateforme | Ressources supplémentaires | Download | Prise en main |
| :-- | :-- | :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [Microsoft ADO.NET pour SQL Server](https://docs.microsoft.com/sql/connect/ado-net/microsoft-ado-net-for-sql-server) | [Télécharger](https://www.microsoft.com/net/download/) | [Démarrer](https://www.microsoft.com/sql-server/developer-get-started/csharp/ubuntu)
| Java | Windows, Linux, macOS | [Pilote Microsoft JDBC pour SQL Server](http://msdn.microsoft.com/library/mt484311.aspx) | [Télécharger](https://go.microsoft.com/fwlink/?linkid=852460) |  [Démarrer](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu)
| PHP | Windows, Linux, macOS| [Pilote PHP SQL pour SQL Server](http://msdn.microsoft.com/library/dn865013.aspx) | Système d’exploitation : <br/> \*[Windows](https://www.microsoft.com/download/details.aspx?id=55642) <br/> \*[Linux](https://github.com/Microsoft/msphpsql/tree/dev#install-unix) <br/> \*[macOS](https://github.com/Microsoft/msphpsql/tree/dev#install-unix) |  [Démarrer](https://www.microsoft.com/sql-server/developer-get-started/php/ubuntu)
| Node.js | Windows, Linux, macOS | [Pilote Node.js pour SQL Server](http://msdn.microsoft.com/library/mt652093.aspx) | [Installer](https://msdn.microsoft.com/library/mt652094.aspx) |  [Démarrer](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu)
| Python | Windows, Linux, macOS | [Pilote Python SQL](http://msdn.microsoft.com/library/mt652092.aspx) | Choix d’installation : <br/> \*[pymssql](https://msdn.microsoft.com/library/mt694094.aspx) <br/> \*[pyodbc](http://msdn.microsoft.com/library/mt763257.aspx) |  [Démarrer](https://www.microsoft.com/sql-server/developer-get-started/python/ubuntu)
| Ruby | Windows, Linux, macOS | [Pilote Ruby pour SQL Server](http://msdn.microsoft.com/library/mt691981.aspx) | [Installer](https://msdn.microsoft.com/library/mt711041.aspx) | [Démarrer](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu)
| C++ | Windows, Linux, macOS | [Pilote Microsoft ODBC pour SQL Server](https://msdn.microsoft.com/library/mt654048(v=sql.1).aspx) | [Télécharger](https://msdn.microsoft.com/library/mt654048(v=sql.1).aspx) |  

Le tableau suivant répertorie des exemples de frameworks de mappage relationnel objet (ORM) et de frameworks web que les applications clientes peuvent utiliser avec SQL Server exécuté localement ou dans le cloud. Vous pouvez utiliser les frameworks sur Linux, Windows ou Docker, pour vous connecter à SQL Database et SQL Data Warehouse. 

| Langage | Plateforme | ORM(s) |
| :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [Entity Framework](https://docs.microsoft.com/ef)<br>[Entity Framework Core](https://docs.microsoft.com/ef/core/index) |
| Java | Windows, Linux, macOS |[Hibernate ORM](http://hibernate.org/orm)|
| PHP | Windows, Linux | [Laravel (Eloquent)](https://laravel.com/docs/5.0/eloquent) |
| Node.js | Windows, Linux, macOS | [Sequelize ORM](http://docs.sequelizejs.com) |
| Python | Windows, Linux, macOS |[Django](https://www.djangoproject.com/) |
| Ruby | Windows, Linux, macOS | [Ruby on Rails](http://rubyonrails.org/) |
||||

## <a name="related-links"></a>Liens connexes
- [Pilotes SQL Server](http://msdn.microsoft.com/library/mt654049.aspx) utilisés pour se connecter à partir d’applications clientes
- Se connecter à SQL Database :
    - [Connexion à SQL Database à l’aide de .NET (C#)](sql-database-connect-query-dotnet.md)
    - [Connexion à SQL Database à l’aide de PHP sur Windows](sql-database-connect-query-php.md)
    - [Connexion à SQL Database à l’aide de Node.js](sql-database-connect-query-nodejs.md)
    - [Connexion à la base de données SQL à l’aide de Java avec JDBC sous Windows](sql-database-connect-query-java.md)
    - [Connexion à SQL Database à l’aide de Python](sql-database-connect-query-python.md)
    - [Connexion à SQL Database à l’aide de Ruby](sql-database-connect-query-ruby.md)
- Exemples de code de logique de nouvelle tentative :
    - [Connexion résiliente à SQL avec ADO.NET][step-4-connect-resiliently-to-sql-with-ado-net-a78n]
    - [Connexion résiliente à SQL avec PHP][step-4-connect-resiliently-to-sql-with-php-p42h]


<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-to-sql-with-ado-net

[step-4-connect-resiliently-to-sql-with-php-p42h]: https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php

