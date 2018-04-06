---
title: Migrer une base de données MySQL vers Azure Database pour MySQL à l’aide des images mémoire et de la restauration
description: Cet article décrit deux méthodes courantes pour sauvegarder et restaurer des bases de données dans votre base de données Azure pour MySQL en utilisant des outils comme mysqldump, MySQL Workbench et PHPMyAdmin.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 03/20/2018
ms.openlocfilehash: ef35ee881923c69d41b79fd6cb8464c695c614f9
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="migrate-your-mysql-database-to-azure-database-for-mysql-using-dump-and-restore"></a>Migrer une base de données MySQL vers une base de données Azure pour MySQL à l’aide des images mémoire et de la restauration
Cet article décrit deux méthodes courantes pour sauvegarder et restaurer des bases de données dans votre base de données Azure pour MySQL.
- Sauvegarder et restaurer à partir de la ligne de commande (en utilisant mysqldump) 
- Sauvegarder et restaurer à l’aide de PHPMyAdmin 

## <a name="before-you-begin"></a>Avant de commencer
Pour parcourir ce guide pratique, vous aurez besoin des éléments suivants :
- [Créer un serveur de base de données Azure pour MySQL - Portail Azure](quickstart-create-mysql-server-database-using-azure-portal.md)
- Utilitaire de ligne de commande [mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html) installé sur une machine
- MySQL Workbench ([téléchargement de MySQL Workbench](https://dev.mysql.com/downloads/workbench/)), Toad, Navicat ou un autre outil MySQL tiers pour exécuter les commandes de sauvegarde et de restauration

## <a name="use-common-tools"></a>Utiliser des outils courants
Utilisez les outils et utilitaires courants tels que MySQL Workbench, mysqldump, Toad ou Navicat pour vous connecter à distance et restaurer les données dans la base de données Azure pour MySQL. Utilisez ces outils sur votre ordinateur client disposant d’une connexion web pour se connecter à la base de données Azure pour MySQL. Utilisez une connexion chiffrée SSL pour appliquer les meilleures pratiques de sécurité ; consultez également la page [Configurer la connectivité SSL dans la base de données Azure pour MySQL](concepts-ssl-connection-security.md). Il est inutile de déplacer les fichiers d’images mémoire dans un emplacement spécifique du cloud lors de la migration vers la base de données Azure pour MySQL. 

## <a name="common-uses-for-dump-and-restore"></a>Utilisations courantes de la sauvegarde et de la restauration
Vous pouvez utiliser des utilitaires MySQL comme mysqldump et mysqlpump pour sauvegarder et charger des bases de données dans une base de données Azure MySQL dans de nombreux scénarios. Dans d’autres scénarios, vous pouvez utiliser l’approche [d’importation et exportation](concepts-migrate-import-export.md).

- Utilisez les sauvegardes de base de données lors de la migration de la base de données entière. Cette recommandation s’applique pour le déplacement d’une grande quantité de données MySQL ou lorsque vous souhaitez minimiser l’interruption de service pour les applications ou sites en ligne. 
-  Assurez-vous que toutes les tables de la base de données utilisent le moteur de stockage InnoDB lors du chargement de données dans la base de données Azure pour MySQL. La base de données Azure pour MySQL prend en charge le moteur de stockage InnoDB uniquement. Par conséquent, elle ne prend pas en charge les autres moteurs de stockage. Si vos tables sont configurées avec d’autres moteurs de stockage, convertissez-les au format de moteur InnoDB avant la migration vers la base de données Azure pour MySQL.
   Par exemple, si votre site WordPress ou votre application web utilise les tables MyISAM, commencez par convertir ces tables en effectuant une migration vers le format InnoDB avant la restauration vers la base de données Azure pour MySQL. Utilisez la clause `ENGINE=InnoDB` pour définir le moteur utilisé lors de la création d’une table, puis transférez les données dans la table compatible avant la restauration. 

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```
- Pour éviter les problèmes de compatibilité, assurez-vous d’utiliser la même version de MySQL sur les systèmes source et de destination lors de la sauvegarde des bases de données. Par exemple, si votre serveur MySQL existant est en version 5.7, vous devriez effectuer une migration vers une base de données Azure pour MySQL configurée pour exécuter la version 5.7. La commande `mysql_upgrade` ne fonctionne pas avec un serveur de base de données Azure pour MySQL et n’est pas prise en charge. Si vous devez mettre à niveau des versions de MySQL, commencez par sauvegarder ou exporter votre base de données de version inférieure vers une version ultérieure de MySQL dans votre propre environnement. Ensuite, exécutez `mysql_upgrade` avant de tenter une migration vers une base de données Azure pour MySQL.

## <a name="performance-considerations"></a>Considérations relatives aux performances
Pour optimiser les performances, tenez compte des considérations suivantes lors de la sauvegarde de bases de données volumineuses :
-   Utilisez l’option `exclude-triggers` dans mysqldump lors de la sauvegarde de bases de données. Excluez les déclencheurs des fichiers de sauvegarde pour éviter tout déclenchement par les commandes correspondantes pendant la restauration des données. 
-   Utilisez l’option `single-transaction` pour définir le mode d’isolation de transaction sur REPEATABLE READ et envoyer une instruction START TRANSACTION SQL au serveur avant le vidage des données. Le vidage de nombreuses tables en une seule transaction provoque la consommation de stockage supplémentaire pendant la restauration. Les options `single-transaction` et `lock-tables` s’excluent mutuellement, car LOCK TABLES provoque la validation implicite des transactions en attente. Pour vider des tables volumineuses, combinez l’option `single-transaction` avec l’option `quick`. 
-   Utilisez la syntaxe pour lignes multiples `extended-insert` qui inclut plusieurs listes VALUE. Cela génère un fichier de vidage plus petit et accélère les insertions lors du rechargement du fichier.
-  Utilisez l’option `order-by-primary` dans mysqldump lors de la sauvegarde de bases de données, afin que les données soient scriptées selon l’ordre des clés primaires.
-   Utilisez l’option `disable-keys` dans mysqldump lors de la sauvegarde des données pour désactiver les contraintes de clé étrangère avant le chargement. La désactivation des vérifications de clé étrangère offre des gains de performances. Activez les contraintes et vérifiez les données après le chargement pour garantir l’intégrité référentielle.
-   Utilisez des tables partitionnées le cas échéant.
-   Chargez les données en parallèle. Évitez tout parallélisme excessif, qui vous ferait atteindre une limite de ressources, et surveillez les ressources à l’aide des mesures disponibles dans le Portail Azure. 
-   Utilisez l’option `defer-table-indexes` dans mysqlpump lors de la sauvegarde des bases de données, afin que l’index soit créé après le chargement des données de tables.

## <a name="create-a-backup-file-from-the-command-line-using-mysqldump"></a>Créer un fichier de sauvegarde en ligne de commande avec mysqldump
Pour sauvegarder une base de données MySQL existante sur le serveur local ou sur une machine virtuelle, exécutez la commande suivante : 
```bash
$ mysqldump --opt -u [uname] -p[pass] [dbname] > [backupfile.sql]
```

Voici les paramètres à fournir :
- [uname] le nom d’utilisateur de votre base de données ; 
- [pass] le mot de passe de votre base de données (remarque : il n’y a pas d’espace entre -p et le mot de passe) ; 
- [dbname] le nom de votre base de données ; 
- [backupfile.sql] le nom du fichier de sauvegarde de votre base de données ; 
- [--opt] l’option mysqldump. 

Par exemple, pour sauvegarder une base de données nommée « testdb » sur votre serveur MySQL avec le nom d’utilisateur « testuser » et sans mot de passe dans un fichier testdb_backup.sql, utilisez la commande suivante. La commande sauvegarde la base de données `testdb` dans un fichier appelé `testdb_backup.sql`, qui contient toutes les instructions SQL nécessaires pour recréer la base de données. 

```bash
$ mysqldump -u root -p testdb > testdb_backup.sql
```
Pour sélectionner certaines tables en particulier à sauvegarder dans votre base de données, listez les noms de table en les séparant par des espaces. Par exemple, pour sauvegarder seulement les tables table1 et table2 de « testdb », suivez cet exemple : 
```bash
$ mysqldump -u root -p testdb table1 table2 > testdb_tables_backup.sql
```

Pour sauvegarder plusieurs bases de données à la fois, utilisez le commutateur --database et listez les noms de bases de données en les séparant par des espaces. 
```bash
$ mysqldump -u root -p --databases testdb1 testdb3 testdb5 > testdb135_backup.sql 
```
Pour sauvegarder toutes les bases de données du serveur en même temps, il est recommandé d’utiliser l’option --all-databases.
```bash
$ mysqldump -u root -p --all-databases > alldb_backup.sql 
```

## <a name="create-a-database-on-the-target-azure-database-for-mysql-server"></a>Créer une base de données sur le serveur cible de base de données Azure pour MySQL
Créez une base de données vide sur le serveur cible de base de données Azure pour MySQL vers lequel vous souhaitez effectuer la migration des données. Utilisez un outil comme MySQL Workbench, Toad ou Navicat pour créer la base de données. La base de données peut avoir le même nom que celle qui contient les données capturées, mais vous pouvez également créer une base de données avec un autre nom.

Pour vous connecter, repérez les informations de connexion dans la **Vue d’ensemble** de votre Azure Database pour MySQL.

![Trouver les informations de connexion dans le Portail Azure](./media/concepts-migrate-dump-restore/1_server-overview-name-login.png)

Ajoutez les informations de connexion à MySQL Workbench.

![Chaîne de connexion MySQL Workbench](./media/concepts-migrate-dump-restore/2_setup-new-connection.png)


## <a name="restore-your-mysql-database-using-command-line-or-mysql-workbench"></a>Restaurer votre base de données MySQL à l’aide d’une ligne de commande ou de MySQL Workbench
Une fois que vous avez créé la base de données cible, vous pouvez utiliser la commande mysql ou MySQL Workbench pour restaurer les données dans la base de données créée à cet effet à partir du fichier d’image mémoire.
```bash
mysql -h [hostname] -u [uname] -p[pass] [db_to_restore] < [backupfile.sql]
```
Dans cet exemple, vous restaurez les données dans la base de données nouvellement créée sur le serveur cible de base de données Azure pour MySQL.
```bash
$ mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p testdb < testdb_backup.sql
```

## <a name="export-using-phpmyadmin"></a>Exporter avec PHPMyAdmin
Pour exporter, vous pouvez utiliser l’outil courant phpMyAdmin, que vous avez peut-être déjà installé localement dans votre environnement. Pour exporter votre base de données MySQL avec PHPMyAdmin :
- Ouvrez phpMyAdmin.
- Sélectionnez votre base de données. Cliquez sur le nom de la base de données dans la liste de gauche. 
- Cliquer sur le lien **Exporter**. Une nouvelle page s’affiche, indiquant la sauvegarde de la base de données.
- Dans la zone Exporter, cliquez sur le lien **Sélectionner tout** pour choisir les tables de votre base de données. 
- Dans la zone des options SQL, cliquez sur les options appropriées. 
- Cliquez sur l’option **Enregistrer en tant que fichier** et sur l’option de compression correspondante, puis sur le bouton **OK**. Une boîte de dialogue vous invite à enregistrer le fichier en local.

## <a name="import-using-phpmyadmin"></a>Importer avec PHPMyAdmin
L’importation d’une base de données est similaire à l’exportation. Effectuez les actions suivantes :
- Ouvrez phpMyAdmin. 
- Dans la page de configuration phpMyAdmin, cliquez sur **Ajouter** pour ajouter votre serveur de base de données Azure pour MySQL. Indiquez les détails et informations de connexion.
- Créez une base de données en lui donnant un nom approprié et sélectionnez-la à gauche de l’écran. Pour réécrire la base de données existante, cliquez sur le nom de la base de données, activez toutes les cases à cocher en regard des noms de table et sélectionnez **Supprimer** pour supprimer les tables existantes. 
- Cliquez sur le lien **SQL** pour afficher la page dans laquelle vous pouvez taper des commandes SQL ou charger votre fichier SQL. 
- Utilisez le bouton **Parcourir** pour trouver le fichier de base de données. 
- Cliquez sur le bouton **OK** pour exporter la sauvegarde, exécuter les commandes SQL et recréer votre base de données.

## <a name="next-steps"></a>Étapes suivantes
[Connectez les applications à la base de données Azure pour MySQL](./howto-connection-string.md)
