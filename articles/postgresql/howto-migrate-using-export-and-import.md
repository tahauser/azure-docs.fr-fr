---
title: "Migration d’une base de données par importation et exportation dans Azure Database pour PostgreSQL | Microsoft Docs"
description: "Décrit comment extraire une base de données PostgreSQL dans un fichier de script et importer les données dans la base de données cible à partir de ce fichier."
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 11/03/2017
ms.openlocfilehash: ddbfd9ef8b2ae4c3c851afc18b010b234b654c81
ms.sourcegitcommit: e19f6a1709b0fe0f898386118fbef858d430e19d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/13/2018
---
# <a name="migrate-your-postgresql-database-using-export-and-import"></a>Migration de votre base de données PostgreSQL par exportation et importation
Vous pouvez utiliser [pg_dump](https://www.postgresql.org/docs/9.3/static/app-pgdump.html) pour extraire une base de données PostgreSQL dans un fichier de script et [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html) pour importer les données dans la base de données cible à partir de ce fichier.

## <a name="prerequisites"></a>Conditions préalables
Pour parcourir ce guide pratique, vous avez besoin des éléments suivants :
- Un [serveur Azure Database pour PostgreSQL](quickstart-create-server-database-portal.md) avec des règles de pare-feu autorisant l’accès et la base de données sous-jacente.
- Utilitaire de ligne de commande [pg_dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html) installé
- Utilitaire de ligne de commande [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html) installé

Suivez ces étapes pour exporter et importer votre base de données PostgreSQL.

## <a name="create-a-script-file-using-pgdump-that-contains-the-data-to-be-loaded"></a>Création d’un fichier de script à l’aide de pg_dump qui contient les données à charger
Pour exporter votre base de données PostgreSQL existante en local ou dans une machine virtuelle vers un fichier de script sql, exécutez la commande suivante dans votre environnement existant :
```bash
pg_dump –-host=<host> --username=<name> --dbname=<database name> --file=<database>.sql
```
Par exemple, si vous avez un serveur local contenant une base de données appelée **testdb** :
```bash
pg_dump --host=localhost --username=masterlogin --dbname=testdb --file=testdb.sql
```

## <a name="import-the-data-on-target-azure-database-for-postgresql"></a>Importation des données dans la base de données cible Azure Database pour PostgreSQL
Vous pouvez utiliser la ligne de commande psql et le paramètre --dbname (-d) pour importer les données dans le serveur Azure Database pour PostrgeSQL et charger les données à partir du fichier sql.
```bash
psql --file=<database>.sql --host=<server name> --port=5432 --username=<user@servername> --dbname=<target database name>
```
Cet exemple utilise l’utilitaire psql et un fichier de script nommé **testdb.sql** de l’étape précédente pour importer les données dans la base de données **mypgsqldb** sur le serveur cible **mypgserver-20170401.postgres.database.azure.com**.
```bash
psql --file=testdb.sql --host=mypgserver-20170401.database.windows.net --port=5432 --username=mylogin@mypgserver-20170401 --dbname=mypgsqldb
```

## <a name="next-steps"></a>étapes suivantes
- Pour migrer une base de données PostgreSQL par vidage et restauration, consultez la rubrique [Migration de votre base de données PostgreSQL par vidage et exportation](howto-migrate-using-dump-and-restore.md)
