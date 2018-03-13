---
title: "Créer des utilisateurs dans Azure Database pour PostgreSQL"
description: "Cet article décrit comment vous pouvez créer des comptes d’utilisateurs pour interagir avec un serveur Azure Database pour PostgreSQL."
services: postgresql
author: jasonwhowell
ms.author: jasonh
editor: jasonwhowell
manager: jhubbard
ms.service: postgresql-database
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 87a73929185112190d5dd6698e014db225ebc08e
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2018
---
# <a name="create-users-in-azure-database-for-postgresql-server"></a>Créer des utilisateurs dans Azure Database pour PostgreSQL 
Cet article décrit comment vous pouvez créer des utilisateurs dans un serveur Azure Database pour PostgreSQL.

## <a name="the-server-admin-account"></a>Compte d’administrateur de serveur
Quand vous avez créé votre serveur Azure Database pour PostgreSQL, vous avez fourni un nom d’utilisateur et un mot de passe d’administrateur de serveur. Pour plus d’informations, vous pouvez suivre la procédure détaillée du [Guide de démarrage rapide](quickstart-create-server-database-portal.md). Étant donné que le nom d’utilisateur administrateur de serveur est un nom personnalisé, vous pouvez rechercher le nom d’utilisateur administrateur de serveur choisi à partir du portail Azure.

Le serveur Azure Database pour PostgreSQL est créé avec les 3 rôles par défaut définis. Vous pouvez voir ces rôles en exécutant la commande : `SELECT rolname FROM pg_roles;`
- azure_pg_admin
- azure_superuser
- votre utilisateur administrateur de serveur

Votre utilisateur administrateur de serveur est un membre du rôle azure_pg_admin. Toutefois, le compte d’administrateur de serveur ne fait pas partie du rôle azure_superuser. Étant donné que ce service est un service PaaS géré, seul Microsoft fait partie du rôle de super utilisateur. 

Le moteur PostgreSQL utilise des privilèges pour contrôler l’accès aux objets de base de données, comme indiqué dans la [documentation du produit PostgreSQL](https://www.postgresql.org/docs/current/static/sql-createrole.html). Dans Azure Database pour PostgreSQL, les privilèges suivants sont accordés à l’utilisateur administrateur de serveur : LOGIN, NOSUPERUSER, INHERIT, CREATEDB, CREATEROLE, NOREPLICATION

Le compte d’utilisateur administrateur de serveur peut être utilisé pour créer des utilisateurs supplémentaires et accorder à ces utilisateurs le rôle azure_pg_admin. Le compte administrateur de serveur peut être utilisé pour créer des utilisateurs et des rôles possédant moins de privilèges et ayant accès à des schémas de base de données individuels.

## <a name="how-to-create-additional-admin-users-in-azure-database-for-postgresql"></a>Création d’utilisateurs administrateurs supplémentaires dans Azure Database pour PostgreSQL
1. Obtenez les informations de connexion et le nom d’utilisateur administrateur.
   Pour vous connecter à votre serveur de base de données, il vous faut le nom de serveur complet et les informations d’identification de connexion d’administrateur. Vous pouvez facilement localiser le nom du serveur et les informations de connexion sur la page **Vue d’ensemble** ou sur la page **Propriétés** du serveur dans le portail Azure. 

2. Utilisez le compte et le mot de passe d’administrateur pour vous connecter à votre serveur de base de données. Utilisez votre outil préféré client, comme pgAdmin ou psql.
   Si vous n’êtes pas sûr de la procédure de connexion, consultez la section [Se connecter à la base de données PostgreSQL à l’aide de psql dans Cloud Shell](./quickstart-create-server-database-portal.md#connect-to-the-postgresql-database-by-using-psql-in-cloud-shell)

3. Modifiez et exécutez le code SQL suivant. Remplacez votre nouveau nom d’utilisateur pour la valeur de l’espace réservé <new_user> et remplacez le mot de passe d’espace réservé avec votre propre mot de passe fort. 

   ```sql
   CREATE ROLE <new_user> WITH LOGIN NOSUPERUSER INHERIT CREATEDB CREATEROLE NOREPLICATION PASSWORD '<StrongPassword!>';
   
   GRANT azure_pg_admin TO <new_user>;
   ```

## <a name="how-to-create-database-users-in-azure-database-for-postgresql"></a>Création d’utilisateurs de base de données dans Azure Database pour PostgreSQL

1. Obtenez les informations de connexion et le nom d’utilisateur administrateur.
   Pour vous connecter à votre serveur de base de données, il vous faut le nom de serveur complet et les informations d’identification de connexion d’administrateur. Vous pouvez facilement localiser le nom du serveur et les informations de connexion sur la page **Vue d’ensemble** ou sur la page **Propriétés** du serveur dans le portail Azure. 

2. Utilisez le compte et le mot de passe d’administrateur pour vous connecter à votre serveur de base de données. Utilisez votre outil préféré client, comme pgAdmin ou psql.

3. Modifiez et exécutez le code SQL suivant. Remplacez la valeur de l’espace réservé `<db_user>` par votre nouveau nom d’utilisateur prévu, et la valeur de l’espace réservé `<newdb>` par le nom à attribuer à la base de données. Remplacez le mot de passe d’espace réservé par votre propre mot de passe fort. 

   Cette syntaxe de code SQL crée une nouvelle base de données nommée testdb, à titre d’exemple. Elle crée ensuite un nouvel utilisateur dans le service PostgreSQL et accorde des privilèges de connexion à la nouvelle base de données pour cet utilisateur. 

   ```sql
   CREATE DATABASE <newdb>;
   
   CREATE ROLE <db_user> WITH LOGIN NOSUPERUSER INHERIT CREATEDB NOCREATEROLE NOREPLICATION PASSWORD '<StrongPassword!>';
   
   GRANT CONNECT ON DATABASE testdb TO <db_user>;
   ```

4. À l’aide d’un compte administrateur, vous devrez peut-être accorder des privilèges supplémentaires pour sécuriser les objets dans la base de données. Reportez-vous à la section [Documentation sur PostgreSQL](https://www.postgresql.org/docs/current/static/ddl-priv.html) pour en savoir plus sur les rôles et privilèges de base de données. Par exemple :  
   ```sql
   GRANT ALL PRIVILEGES ON DATABASE <newdb> TO <db_user>;
   ```

5. Connectez-vous à votre serveur, en spécifiant la base de données désignée, à l’aide des nouveaux nom d’utilisateur et mot de passe. Cet exemple montre la ligne de commande psql. Cette commande vous invite à entrer le mot de passe pour le nom d’utilisateur. Indiquez votre propre nom de serveur, nom de base de données et nom d’utilisateur.

   ```azurecli-interactive
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=db_user@mydemoserver --dbname=newdb
   ```

## <a name="next-steps"></a>Étapes suivantes
Ouvrez le pare-feu pour les adresses IP des machines des nouveaux utilisateurs pour leur permettre de se connecter : consultez [Créer et gérer des règles de pare-feu Azure Database pour PostgreSQL à l’aide du portail Azure](howto-manage-firewall-using-portal.md) ou [Azure CLI](howto-manage-firewall-using-cli.md).

Pour plus d’informations sur la gestion des comptes d’utilisateurs, consultez la documentation du produit PostgreSQL relative aux [rôles et privilèges de base de données](https://www.postgresql.org/docs/current/static/user-manag.html), à la [syntaxe GRANT](https://www.postgresql.org/docs/current/static/sql-grant.html) et aux [privilèges](https://www.postgresql.org/docs/current/static/ddl-priv.html).
