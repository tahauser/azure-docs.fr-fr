---
title: "Bibliothèques de connexions de la base de données Azure pour PostgreSQL"
description: "Cet article décrit plusieurs bibliothèques et pilotes que les développeurs peuvent utiliser lorsqu’ils codent des applications pour se connecter à une base de données Azure pour PostgreSQL et l’interroger."
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 7f0dd217a124f530ba009c9a5588691f604827e5
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2018
---
# <a name="connection-libraries-for-azure-database-for-postgresql"></a>Bibliothèques de connexions de la base de données Azure pour PostgreSQL
Cet article liste les bibliothèques et les pilotes qui sont à la disposition des développeurs pour développer des applications qui se connectent à Azure Database pour PostgreSQL et l’interrogent.

## <a name="client-interfaces"></a>Interfaces client
La plupart des bibliothèques clientes de langages qui permettent de se connecter au serveur PostgreSQL sont des projets externes, distribués de manière indépendante. Les bibliothèques répertoriées sont prises en charge sur les plateformes Windows, Linux et Mac, pour la connexion à Azure Database pour PostgreSQL. Plusieurs exemples de démarrage rapide sont regroupés à la section Étapes suivantes.

| **Langage** | **Interface client** | **Informations supplémentaires** | **Télécharger** |
|--------------|----------------------------------------------------------------|-------------------------------------|--------------------------------------------------------------------|
| Python | [psycopg](http://initd.org/psycopg/) | Compatible DB API 2.0 | [Télécharger](http://initd.org/psycopg/download/) |
| PHP | [php-pgsql](https://php.net/manual/en/book.pgsql.php) | Extension de base de données | [Installer](https://secure.php.net/manual/en/pgsql.installation.php) |
| Node.js | [Package pg npm](https://www.npmjs.com/package/pg) | Client non bloquant JavaScript pur | [Installer](https://www.npmjs.com/package/pg) |
| Java | [JDBC](http://jdbc.postgresql.org/) | Pilote JDBC de type 4 | [Télécharger](https://jdbc.postgresql.org/download.html)  |
| Ruby | [Pg gem](https://deveiate.org/code/pg/) | Interface Ruby | [Télécharger](https://rubygems.org/downloads/pg-0.20.0.gem) |
| Go | [Package pq](https://godoc.org/github.com/lib/pq) | Pilote Go Postgres pur | [Installer](https://github.com/lib/pq/blob/master/README.md) |
| C\#/ .NET | [Npgsql](http://www.npgsql.org/) | Fournisseur de données ADO.NET | [Télécharger](https://www.microsoft.com/net/) |
| ODBC | [psqlODBC](https://odbc.postgresql.org/) | Pilote ODBC | [Télécharger](http://www.postgresql.org/ftp/odbc/versions/) |
| C | [libpq](https://www.postgresql.org/docs/9.6/static/libpq.html) | Interface principale du langage C | Inclus |
| C++ | [libpqxx](http://pqxx.org/) | Interface C++ remaniée | [Télécharger](http://pqxx.org/download/software/) |

## <a name="next-steps"></a>Étapes suivantes
Lisez ces guides de démarrage rapide pour savoir comment se connecter à Azure Database for PostgreSQL et l’interroger dans le langage de votre choix :

[Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md) | [PHP](./connect-php.md) | [.NET (C#)](./connect-csharp.md) | [Go](./connect-go.md)
