---
title: "Bibliothèques de connexions pour Azure Database pour MySQL"
description: "Cet article répertorie chaque bibliothèque ou pilote que les programmes clients peuvent utiliser lors de la connexion à Azure Database pour MySQL."
services: mysql
author: mswutao
ms.author: wutao
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: c22bb9b31e7623daa635f82256e28ed466070481
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2018
---
# <a name="connection-libraries-for-azure-database-for-mysql"></a>Bibliothèques de connexions pour Azure Database pour MySQL
Cet article répertorie chaque bibliothèque ou pilote que les programmes clients peuvent utiliser lors de la connexion à Azure Database pour MySQL.

## <a name="client-interfaces"></a>Interfaces client
MySQL offre une connectivité de pilote de base de données standard pour l’utilisation de MySQL avec des applications et outils qui sont compatibles avec les normes ODBC et JDBC du secteur. Tout système qui fonctionne avec ODBC ou JDBC peut utiliser MySQL.

| **Langage** | **Plateforme** | **Ressource supplémentaire** | **Télécharger** |
| :----------- | :------------| :-----------------------| :------------|
| PHP | Windows, Linux | [Pilote natif MySQL pour PHP - mysqlnd](https://dev.mysql.com/downloads/connector/php-mysqlnd/) | [Télécharger](http://php.net/downloads.php) |
| ODBC | Plateformes Windows, Linux, Mac OS X et Unix | [Guide de développement pour MySQL Connector/ODBC](https://dev.mysql.com/doc/connector-odbc/en/) | [Télécharger](https://dev.mysql.com/downloads/connector/odbc/) |
| ADO.NET | Windows | [Guide de développement pour MySQL Connector/Net](https://dev.mysql.com/doc/connector-net/en/) | [Télécharger](https://dev.mysql.com/downloads/connector/net/) |
| JDBC | Indépendant de la plateforme | [Guide de développement pour MySQL Connector/J 5.1](https://dev.mysql.com/doc/connector-j/5.1/en/) | [Télécharger](https://dev.mysql.com/downloads/connector/j/) |
| Node.js | Windows, Linux, Mac OS X | [sidorares/node-mysql2](https://github.com/sidorares/node-mysql2/tree/master/documentation) | [Télécharger](https://github.com/sidorares/node-mysql2) |
| Python | Windows, Linux, Mac OS X | [Guide de développement pour MySQL Connector/Python](https://dev.mysql.com/doc/connector-python/en/) | [Télécharger](https://dev.mysql.com/downloads/connector/python/) |
| C++ | Windows, Linux, Mac OS X | [Guide de développement pour MySQL Connector/C++](https://dev.mysql.com/doc/connector-cpp/en/) | [Télécharger](https://dev.mysql.com/downloads/connector/python/) |
| C | Windows, Linux, Mac OS X | [Guide de développement pour MySQL Connector/C](https://dev.mysql.com/doc/connector-c/en/) | [Télécharger](https://dev.mysql.com/downloads/connector/c/)
| Perl | Plateformes Windows, Linux, Mac OS X et Unix | [DBD::MySQL](https://metacpan.org/pod/DBD::mysql) | [Télécharger](https://metacpan.org/pod/DBD::mysql) |


## <a name="next-steps"></a>Étapes suivantes
Lisez ces guides de démarrage rapide pour savoir comment vous connecter à Azure Database pour MySQL et l’interroger dans le langage de votre choix :

[PHP](./connect-php.md) | [Java](./connect-java.md) |  [.NET (C#)](./connect-csharp.md) | [Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Ruby](./connect-ruby.md) | [C++](connect-cpp.md) | [Go](./connect-go.md)

