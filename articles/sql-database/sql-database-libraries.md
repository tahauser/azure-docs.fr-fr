---
title: "Bibliothèques de connexions pour SQL Database | Microsoft Docs"
description: "Fournit des liens de téléchargement de modules qui permettent de se connecter à SQL Server et SQL Database à partir d’un large éventail de langages de programmation côté client."
services: sql-database
documentationcenter: 
author: MightyPen
manager: jhubbard
editor: genemi
ms.assetid: 13d899d3-cf46-4e4d-8919-cf4b41ca836d
ms.service: sql-database
ms.custom: develop apps
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.author: genemi
ms.openlocfilehash: 44c4491e7c198eec43a31480ef3df748626b7b75
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/30/2017
---
# <a name="connectivity-libraries-and-frameworks-for-microsoft-sql-server"></a>Bibliothèques et infrastructures de connectivité pour Microsoft SQL Server

Découvrez nos [Didacticiels de mise en route](http://aka.ms/sqldev) pour prendre en main rapidement des langages de programmation tels que C#, Java, Node.js, PHP et Python, et créer une application à l’aide de SQL Server sur Linux, Windows ou de Docker sur macOS.

Le tableau ci-dessous répertorie les bibliothèques de connectivité ou *pilotes* que les applications clientes peuvent utiliser dans différents langages pour se connecter à Microsoft SQL Server localement ou dans le cloud, sur Linux, Windows ou Docker, ainsi qu’à Azure SQL Database et Azure SQL Data Warehouse. 

| Langage | Plateforme | Ressources supplémentaires | Télécharger | Prise en main |
| :-- | :-- | :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [Microsoft ADO.NET pour SQL Server](https://docs.microsoft.com/sql/connect/ado-net/microsoft-ado-net-for-sql-server) | [Télécharger](https://www.microsoft.com/net/download/) | [Prise en main](https://www.microsoft.com/en-us/sql-server/developer-get-started/csharp/ubuntu)
| Java | Windows, Linux, macOS | [Pilote JDBC Microsoft pour SQL Server](http://msdn.microsoft.com/library/mt484311.aspx) | [Télécharger](https://go.microsoft.com/fwlink/?linkid=852460) |  [Prise en main](https://www.microsoft.com/en-us/sql-server/developer-get-started/java/ubuntu)
| PHP | Windows, Linux, macOS| [Pilote PHP SQL pour SQL Server](http://msdn.microsoft.com/library/dn865013.aspx) | Système d’exploitation : <br/> \*[Windows](https://www.microsoft.com/download/details.aspx?id=20098) <br/> \*[Linux](https://github.com/Microsoft/msphpsql/tree/dev#install-unix) <br/> \*[macOS](https://github.com/Microsoft/msphpsql/tree/dev#install-unix) |  [Prise en main](https://www.microsoft.com/en-us/sql-server/developer-get-started/php/ubuntu)
| Node.js | Windows, Linux, macOS | [Pilote Node.js pour SQL Server](http://msdn.microsoft.com/library/mt652093.aspx) | [Installer](https://msdn.microsoft.com/library/mt652094.aspx) |  [Prise en main](https://www.microsoft.com/en-us/sql-server/developer-get-started/node/ubuntu)
| Python | Windows, Linux, macOS | [Pilote SQL Python](http://msdn.microsoft.com/library/mt652092.aspx) | Choix d’installation : <br/> \*[pymssql](https://msdn.microsoft.com/library/mt694094.aspx) <br/> \*[pyodbc](http://msdn.microsoft.com/library/mt763257.aspx) |  [Prise en main](https://www.microsoft.com/en-us/sql-server/developer-get-started/python/ubuntu)
| Ruby | Windows, Linux, macOS | [Pilote Ruby pour SQL Server](http://msdn.microsoft.com/library/mt691981.aspx) | [Installer](https://msdn.microsoft.com/library/mt711041.aspx) | [Prise en main](https://www.microsoft.com/en-us/sql-server/developer-get-started/ruby/ubuntu)
| C++ | Windows, Linux, macOS | [Pilote ODBC Microsoft pour SQL Server](https://msdn.microsoft.com/en-us/library/mt654048(v=sql.1).aspx) | [Télécharger](https://msdn.microsoft.com/en-us/library/mt654048(v=sql.1).aspx) |  

Le tableau ci-dessous répertorie quelques infrastructures ORM (Object Relational Mapping) et web que les applications clientes peuvent utiliser avec Microsoft SQL Server localement ou dans le cloud, sur Linux, Windows ou Docker, ainsi qu’avec Azure SQL Database et Azure SQL Data Warehouse. 

| Langage | Plateforme | ORM(s) |
| :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [Entity Framework](https://docs.microsoft.com/en-us/ef)<br>[Entity Framework Core](https://docs.microsoft.com/en-us/ef/core/index) |
| Java | Windows, Linux, macOS |[Hibernate ORM](http://hibernate.org/orm)|
| PHP | Windows, Linux | [Laravel (Eloquent)](https://laravel.com/docs/5.0/eloquent) |
| Node.js | Windows, Linux, macOS | [Sequelize ORM](http://docs.sequelizejs.com) |
| Python | Windows, Linux, macOS |[Django](https://www.djangoproject.com/) |
| Ruby | Windows, Linux, macOS | [Ruby on Rails](http://rubyonrails.org/) |
||||

## <a name="related-links"></a>Liens connexes
- [Pilotes SQL Server](http://msdn.microsoft.com/library/mt654049.aspx) pour se connecter à partir d’applications clientes
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

