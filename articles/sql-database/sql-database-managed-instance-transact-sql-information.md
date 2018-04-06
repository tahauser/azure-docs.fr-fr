---
title: Différences T-SQL sur Azure SQL Database Managed Instance | Microsoft Docs
description: Cet article décrit les différences T-SQL entre Azure SQL Database Managed Instance et SQL Server.
services: sql-database
author: jovanpop-msft
ms.reviewer: carlrab, bonova
ms.service: sql-database
ms.custom: managed instance
ms.topic: article
ms.date: 03/19/2018
ms.author: jovanpop
manager: craigg
ms.openlocfilehash: b633c3c4a4f476cb8e89afde8adeb94558643d4b
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-sql-database-managed-instance-t-sql-differences-from-sql-server"></a>Différences T-SQL entre Azure SQL Database Managed Instance et SQL Server 

Azure SQL Database Managed Instance (préversion) fournit une haute compatibilité avec le moteur de base de données SQL Server local. La plupart des fonctionnalités du moteur de base de données SQL Server local sont prises en charge par Managed Instance. Comme il existe toujours des différences de syntaxe et de comportement, cet article résume et explique ces différences.
 - [Différences T-SQL et fonctionnalités non prises en charge](#Differences)
 - [Fonctionnalités qui se comportent différemment dans Managed Instance](#Changes)
 - [Limitations temporaires et problèmes connus](#Issues)

## <a name="Differences"></a> Différences T-SQL par rapport à SQL Server 

Cette section résume les principales différences de syntaxe et de comportement T-SQL entre Managed Instance et le moteur de base de données SQL Server local, ainsi que les fonctionnalités non prises en charge.

### <a name="always-on-availability"></a>Disponibilité Always On

[Haute disponibilité](sql-database-high-availability.md) est intégré à Managed Instance et ne peut pas être contrôlé par les utilisateurs. Les instructions suivantes ne sont pas prises en charge :
 - [CREATE ENDPOINT … FOR DATABASE_MIRRORING](https://docs.microsoft.com/sql/t-sql/statements/create-endpoint-transact-sql)
 - [CREATE AVAILABILITY GROUP](https://docs.microsoft.com/sql/t-sql/statements/create-availability-group-transact-sql)
 - [ALTER AVAILABILITY GROUP](https://docs.microsoft.com/sql/t-sql/statements/alter-availability-group-transact-sql)
 - [DROP AVAILABILITY GROUP](https://docs.microsoft.com/sql/t-sql/statements/drop-availability-group-transact-sql)
 - Clause [SET HADR](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-hadr) de l’instruction [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql)

### <a name="auditing"></a>Audit 
 
Les principales différences entre SQL Audit dans Managed Instance, Azure SQL Database et SQL Server en local sont :
- Dans Managed Instance, SQL Audit fonctionne au niveau du serveur et stocke les fichiers `.xel` sur le compte de Stockage Blob Azure.  
- Dans Azure SQL Database, SQL Audit fonctionne au niveau de la base de données.
- Dans SQL Server en local/machine virtuelle, SQL Audit fonctionne au niveau du serveur, mais enregistre les événements sur les fichiers journaux des événements système/windows.  
  
L’audit XEvent sur Managed Instance prend en charge les cibles de Stockage Blob Azure. Les journaux de fichiers et de Windows ne sont pas pris en charge.    
 
Les principales différences de syntaxe `CREATE AUDIT` pour l’audit du Stockage Blob Azures sont :
- Une nouvelle syntaxe `TO URL` est fournie et vous permet de spécifier l’URL du conteneur du Stockage Blob Azure où les fichiers `.xel` seront placés 
- La syntaxe `TO FILE` n’est pas prise en charge, car Managed Instance ne peut pas accéder aux partages de fichiers Windows. 
 
Pour plus d'informations, consultez les pages suivantes :  
- [CREATE SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-transact-sql)  
- [ALTER SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/alter-server-audit-transact-sql) 
- [Audit](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine)     

### <a name="backup"></a>Sauvegarde 

Managed Instance dispose de sauvegardes automatiques, et permet aux utilisateurs de créer des sauvegardes complètes `COPY_ONLY` des bases de données. Les sauvegardes différentielles, les sauvegardes de journaux et de captures instantanées de fichiers ne sont pas prises en charge.  
- Managed Instance peut sauvegarder une base de données uniquement vers un compte Stockage Blob Azure : 
 - Seul `BACKUP TO URL` est pris en charge 
 - `FILE`, `TAPE`, et les unités de sauvegarde ne sont pas pris en charge  
- La plupart des options générales `WITH` sont prises en charge 
 - `COPY_ONLY` est obligatoire
 - `FILE_SNAPSHOT` non pris en charge  
 - Options de bande : `REWIND`, `NOREWIND`, `UNLOAD`, et `NOUNLOAD` ne sont pas pris en charge 
 - Options spécifiques au journal : `NORECOVERY`, `STANDBY`, et `NO_TRUNCATE` ne sont pas pris en charge 
 
Limites :  
- Managed Instance peut sauvegarder une base de données vers une sauvegarde comprenant jusqu’à 32 bandes, ce qui est suffisant pour les bases de données jusqu’à 4 To si la compression de sauvegarde est utilisée.
- La taille maximale de la bande de sauvegarde est 195 Go (taille maximale d’un blob). Augmentez le nombre de bandes dans la commande de sauvegarde pour réduire la taille de bande individuelle et ne pas dépasser cette limite. 

> [!TIP]
> Pour contourner cette limitation en local, sauvegardez sur `DISK` au lieu de sauvegarder sur `URL`, chargez le fichier de sauvegarde vers le blob, puis restaurez. La restauration prend en charge de plus gros fichiers car un autre type de blob est utilisé.  

Pour plus d’informations sur les sauvegardes à l’aide de T-SQL, consultez [BACKUP](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql).

### <a name="buffer-pool-extension"></a>Extension du pool de mémoires tampons 
 
- L’[extension du pool de mémoires tampons](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension) n’est pas prise en charge.
- `ALTER SERVER CONFIGURATION SET BUFFER POOL EXTENSION` n’est pas pris en charge. Consultez [ALTER SERVER CONFIGURATION](https://docs.microsoft.com/sql/t-sql/statements/alter-server-configuration-transact-sql). 
 
### <a name="bulk-insert--openrowset"></a>Insertion en bloc/openrowset

Managed Instance ne peut pas accéder aux partages de fichiers et aux dossiers Windows, les fichiers doivent donc être importés à partir du Stockage Blob Azure.
- `DATASOURCE` est requis dans la commande `BULK INSERT` lors de l’importation des fichiers depuis le Stockage Blob Azure. Consultez [BULK INSERT](https://docs.microsoft.com/sql/t-sql/statements/bulk-insert-transact-sql).
- `DATASOURCE` est requis dans la fonction `OPENROWSET`lorsque vous lisez le contenu d’un fichier à partir du Stockage Blob Azure. Consultez [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql).
 
### <a name="certificates"></a>Certificats 

Managed Instance ne peut pas accéder aux partages de fichiers et aux dossiers Windows, les contraintes suivantes s’appliquent donc : 
- Le fichier `CREATE FROM`/`BACKUP TO` n’est pas pris en charge pour les certificats
- Le certificat `CREATE`/`BACKUP` de `FILE`/`ASSEMBLY` n’est pas pris en charge. Les fichiers de clés privées ne peuvent pas être utilisés.  
 
Consultez [CREATE CERTIFICATE](https://docs.microsoft.com/sql/t-sql/statements/create-certificate-transact-sql) et [BACKUP CERTIFICATE](https://docs.microsoft.com/sql/t-sql/statements/backup-certificate-transact-sql).  
  
> [!TIP]
> Solution de contournement : certificat de script/clé privée, stockez en tant que fichier .sql et créez à partir du fichier binaire : 
> 
> ``` 
CREATE CERTIFICATE  
 FROM BINARY = asn_encoded_certificate    
WITH PRIVATE KEY ( <private_key_options> ) 
>```   
 
### <a name="clr"></a>CLR 

Managed Instance ne peut pas accéder aux partages de fichiers et aux dossiers Windows, les contraintes suivantes s’appliquent donc : 
- Seul `CREATE ASSEMBLY FROM BINARY` est pris en charge. Consultez [CREATE ASSEMBLY FROM BINARY](https://docs.microsoft.com/sql/t-sql/statements/create-assembly-transact-sql).  
- `CREATE ASSEMBLY FROM FILE` n’est pas pris en charge. Consultez [CREATE ASSEMBLY FROM FILE](https://docs.microsoft.com/sql/t-sql/statements/create-assembly-transact-sql).
- `ALTER ASSEMBLY` ne peut pas référencer des fichiers. Consultez [ALTER ASSEMBLY](https://docs.microsoft.com/sql/t-sql/statements/alter-assembly-transact-sql).
 
### <a name="compatibility-levels"></a>Niveaux de compatibilité 
 
- Les niveaux de compatibilité pris en charge sont : 100, 110, 120, 130, 140  
- Les niveaux de compatibilité inférieurs à 100 ne sont pas pris en charge. 
- Le niveau de compatibilité par défaut est de 140 pour les nouvelles bases de données. Pour les bases de données restaurées, le niveau de compatibilité reste inchangé s’il était de 100 et plus.

Consultez [Niveau de compatibilité ALTER TABLE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level).
 
### <a name="credential"></a>Informations d'identification 
 
Seuls Azure Key Vault et les identités `SHARED ACCESS SIGNATURE` sont pris en charge. Les utilisateurs Windows ne sont pas pris en charge.
 
Consultez [CREATE CREDENTIAL](https://docs.microsoft.com/sql/t-sql/statements/create-credential-transact-sql) et [ALTER CREDENTIAL](https://docs.microsoft.com/sql/t-sql/statements/alter-credential-transact-sql). 
 
### <a name="cryptographic-providers"></a>Fournisseurs de chiffrement

Managed Instance ne peut pas accéder aux fichiers, les fournisseurs de chiffrement ne peuvent donc pas être créés :
- `CREATE CRYPTOGRAPHIC PROVIDER` n’est pas pris en charge. Consultez [CREATE CRYPTOGRAPHIC PROVIDER](https://docs.microsoft.com/sql/t-sql/statements/create-cryptographic-provider-transact-sql).
- `ALTER CRYPTOGRAPHIC PROVIDER` n’est pas pris en charge. Consultez [ALTER CRYPTOGRAPHIC PROVIDER](https://docs.microsoft.com/sql/t-sql/statements/alter-cryptographic-provider-transact-sql). 

### <a name="collation"></a>Collation 
 
Le classement du serveur est `SQL_Latin1_General_CP1_CI_AS` et ne peut pas être modifié. Consultez [Classements](https://docs.microsoft.com/sql/t-sql/statements/collations).
 
### <a name="database-options"></a>Options de la base de données 
 
- Les fichiers journaux multiples ne sont pas pris en charge. 
- Les objets en mémoire ne sont pas pris en charge dans le niveau de service usage général.  
- Il existe une limite de 280 fichiers par instance, ce qui implique un maximum de 280 fichiers par base de données. Les fichiers de données et de journaux sont comptabilisés dans cette limite.  
- La base de données ne peut pas contenir de groupes de fichiers contenant des données flux de fichier.  La restauration échoue si le fichier.bak contient des données `FILESTREAM`.  
- Chaque fichier est placé dans Azure Stockage Premium. L’E/S et le débit par fichier dépendent de la taille de chaque fichier, comme pour les disques Azure Stockage Premium. Consultez [Performances du disque Premium Azure](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#premium-storage-disk-sizes)  
 
#### <a name="create-database-statement"></a>Instruction CREATE DATABASE

Les éléments suivants sont des limitations `CREATE DATABASE` : 
- Les fichiers et les groupes de fichiers ne peuvent pas être définis.  
- L’option `CONTAINMENT` n’est pas prise en charge.  
- Les options `WITH` ne sont pas prises en charge.  
   > [!TIP]
   > Comme solution de contournement, utilisez `ALTER DATABASE` après `CREATE DATABASE` pour définir les options de la base de données de façon à ajouter des fichiers ou pour définir la relation contenant-contenu.  

- L’option `FOR ATTACH` n’est pas prise en charge 
- L’option `AS SNAPSHOT OF` n’est pas prise en charge 

Pour plus d’informations, consultez [CREATE DATABASE](https://docs.microsoft.com/sql/t-sql/statements/create-database-sql-server-transact-sql).

#### <a name="alter-database-statement"></a>Instruction ALTER DATABASE

Certaines propriétés de fichier ne peuvent pas être définies ou modifiées :
- Le chemin d’accès de fichier ne peut pas être spécifié dans l’instruction T-SQL `ALTER DATABASE ADD FILE (FILENAME='path')`. Enlevez `FILENAME` du script car Managed Instance place les fichiers automatiquement.  
- Le nom de fichier ne peut pas être modifié à l’aide de l’instruction `ALTER DATABASE`.

Les options suivantes sont définies par défaut et ne peuvent pas être modifiées : 
- `MULTI_USER` 
- `ENABLE_BROKER ON` 
- `AUTO_CLOSE OFF` 

Les options suivantes ne peuvent pas être modifiées : 
- `AUTO_CLOSE` 
- `AUTOMATIC_TUNING(CREATE_INDEX=ON|OFF)` 
- `AUTOMATIC_TUNING(DROP_INDEX=ON|OFF)` 
- `DISABLE_BROKER` 
- `EMERGENCY` 
- `ENABLE_BROKER` 
- `FILESTREAM` 
- `HADR`   
- `NEW_BROKER` 
- `OFFLINE` 
- `PAGE_VERIFY` 
- `PARTNER` 
- `READ_ONLY` 
- `RECOVERY BULK_LOGGED` 
- `RECOVERY_SIMPLE` 
- `REMOTE_DATA_ARCHIVE`  
- `RESTRICTED_USER` 
- `SINGLE_USER` 
- `WITNESS`

La modification du nom n’est pas prise en charge.

Pour en savoir plus, consultez [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-file-and-filegroup-options).

### <a name="database-mirroring"></a>Mise en miroir de bases de données

La mise en miroir de bases de données n’est pas prise en charge.
 - Les options `ALTER DATABASE SET PARTNER` et `SET WITNESS` ne sont pas prises en charge.
 - `CREATE ENDPOINT … FOR DATABASE_MIRRORING` n’est pas pris en charge.

Pour plus d’informations, consultez [ALTER DATABASE SET PARTNER AND SET WITNESS](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-database-mirroring) et [CREATE ENDPOINT … FOR DATABASE_MIRRORING](https://docs.microsoft.com/sql/t-sql/statements/create-endpoint-transact-sql).

### <a name="dbcc"></a>DBCC 
 
Les instructions DBCC non documentées activées dans SQL Server ne sont pas prises en charge dans Managed Instance.
- `Trace Flags` ne sont pas pris en charge. Consultez les [indicateurs de trace](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql).
- `DBCC TRACEOFF` n’est pas pris en charge. Consultez [DBCC TRACEOFF](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceoff-transact-sql).
- `DBCC TRACEON` n’est pas pris en charge. Consultez [DBCC TRACEOFF](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql).

### <a name="extended-events"></a>Événements étendus 

Certaines cibles spécifiques de Windows pour les événements XEvent ne sont pas prises en charge :
- `etw_classic_sync target` n’est pas pris en charge. Stockez les fichiers `.xel` sur le Stockage Blob Azure. Consultez [etw_classic_sync target](https://docs.microsoft.com/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#etwclassicsynctarget-target). 
- `event_file target`n’est pas pris en charge. Stockez les fichiers `.xel` sur le Stockage Blob Azure. Consultez [event_file target](https://docs.microsoft.com/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#eventfile-target).

### <a name="external-libraries"></a>Bibliothèques externes

Les bibliothèques R et Python externes en base de données ne sont pas encore prises en charge. Consultez [SQL Server Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/r/sql-server-r-services).

### <a name="filestream-and-filetable"></a>Flux de fichier et Filetable

- Les données flux de fichier ne sont pas prises en charge. 
- La base de données ne peut pas contenir de groupes de fichiers avec des données `FILESTREAM`
- `FILETABLE` n’est pas pris en charge
- Les tables ne peuvent pas avoir de types `FILESTREAM`
- Les fonctions suivantes ne sont pas prises en charge :
 - `GetPathLocator()` 
 - `GET_FILESTREAM_TRANSACTION_CONTEXT()` 
 - `PathName()` 
 - `GetFileNamespacePath()` 
 - `FileTableRootPath()` 

Pour plus d’informations, consultez [FILESTREAM](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) et [FileTables](https://docs.microsoft.com/sql/relational-databases/blob/filetables-sql-server).

### <a name="full-text-semantic-search"></a>Recherche sémantique de texte intégral

[La recherche sémantique](https://docs.microsoft.com/sql/relational-databases/search/semantic-search-sql-server) n’est pas prise en charge.

### <a name="linked-servers"></a>Services liés
 
Les serveurs liés dans Managed Instance prennent en charge un nombre limité de cibles : 
- Cibles prises en charge : SQL Server, SQL Database, Managed Instance et SQL Server sur une machine virtuelle.
- Cibles non prises en charge : fichiers, Analysis Services et autres SGBDR.

Opérations

- Les transactions d’écriture entre instances ne sont pas prises en charge.
- `sp_dropserver` est pris en charge pour supprimer un serveur lié. Consultez [sp_dropserver](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-dropserver-transact-sql).
- La fonction `OPENROWSET` peut être utilisée pour exécuter des requêtes uniquement sur les instances de SQL Server (géré, local ou sur des machines virtuelles). Consultez [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql).
- La fonction `OPENDATASOURCE` peut être utilisée pour exécuter des requêtes uniquement sur les instances de SQL Server (géré, local ou sur des machines virtuelles). Seules les valeurs `SQLNCLI`, `SQLNCLI11`, et `SQLOLEDB` sont prises en charge en tant que fournisseur. Par exemple : `SELECT * FROM OPENDATASOURCE('SQLNCLI', '...').AdventureWorks2012.HumanResources.Employee`. Consultez [OPENDATASOURCE](https://docs.microsoft.com/sql/t-sql/functions/opendatasource-transact-sql).
 
### <a name="logins--users"></a>Connexions/utilisateurs 

- Les connexions SQL créées `FROM CERTIFICATE`, `FROM ASYMMETRIC KEY`, et `FROM SID` sont prises en charge. Consultez [CREATE LOGIN](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql).
- Les connexions Windows créées avec la syntaxe `CREATE LOGIN ... FROM WINDOWS` ne sont pas prises en charge.
- L’utilisateur Azure Active Directory (Azure AD) qui a créé l’instance dispose de [privilèges d’administrateur illimités](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins#unrestricted-administrative-accounts).
- Les utilisateurs au niveau de la base de données Azure Active Directory (Azure AD) non-administrateurs peuvent être créés à l’aide de la syntaxe `CREATE USER ... FROM EXTERNAL PROVIDER`. Consultez [CREATE USER ... FROM EXTERNAL PROVIDER](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins#non-administrator-users)
 
### <a name="polybase"></a>Polybase

Les tables externes référençant les fichiers dans HDFS ou le Stockage Blob Azure ne sont pas prises en charge. Pour plus d’informations sur Polybase, consultez [Polybase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide).

### <a name="replication"></a>Réplication 
 
La réplication n’est pas encore prise en charge. Pour plus d’informations sur la réplication, consultez [Réplication SQL Server](https://docs.microsoft.com/sql/relational-databases/replication/sql-server-replication).
 
### <a name="restore-statement"></a>L’instruction RESTORE 
 
- Syntaxe prise en charge  
   - `RESTORE DATABASE` 
   - `RESTORE FILELISTONLY ONLY` 
   - `RESTORE HEADER ONLY` 
   - `RESTORE LABELONLY ONLY` 
   - `RESTORE VERIFYONLY ONLY` 
- Syntaxe non prise en charge 
   - `RESTORE LOG ONLY` 
   - `RESTORE REWINDONLY ONLY`
- Source  
 - `FROM URL` (Stockage Blob Azure) est l’unique option prise en charge.
 - `FROM DISK`/`TAPE`/l’unité de sauvegarde n’est pas prise en charge.
 - Les jeux de sauvegarde ne sont pas pris en charge. 
- Les options `WITH` ne sont pas prises en charge (`DIFFERENTIAL`, `STATS`, etc.)     
- `ASYNC RESTORE` - la restauration continue même si la connexion cliente s’arrête. Si votre connexion est interrompue, vous pouvez vérifier l’affichage `sys.dm_operation_status` de l’état d’une opération de restauration (ainsi que pour la création et la suppression d’une base de données). Consultez [sys.dm_operation_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database).  
 
Les options de base de données suivantes sont fixées/remplacées et ne peuvent pas être modifiées ultérieurement :  
- `NEW_BROKER` (si le broker n’est pas activé dans le fichier .bak)  
- `ENABLE_BROKER` (si le broker n’est pas activé dans le fichier .bak)  
- `AUTO_CLOSE=OFF` (si une base de données dans le fichier .bak comporte `AUTO_CLOSE=ON`)  
- `RECOVERY FULL` (si une base de données dans le fichier .bak a le mode de récupération `SIMPLE` ou `BULK_LOGGED`)
- Le groupe de fichiers mémoire optimisée est ajouté et appelé XTP s’il n’était pas dans le fichier .bak source  
- Les groupe de fichiers mémoire optimisée existants sont renommés XTP  
- Les options `SINGLE_USER` et `RESTRICTED_USER` sont converties en `MULTI_USER`   
Limites :  
- Les fichiers `.BAK` contenant plusieurs jeux de sauvegarde ne peuvent pas être restaurés. 
- Les fichiers `.BAK` contenant plusieurs fichiers journaux ne peuvent pas être restaurés. 
- La restauration échoue si le fichier.bak contient des données `FILESTREAM`.
- Il est actuellement impossible de restaurer les sauvegardes contenant des bases de données qui ont des objets en mémoire active.  
- Il est actuellement impossible de restaurer les sauvegardes contenant des bases de données où des objets en mémoire ont existé à un moment donné.   
- Il est actuellement impossible de restaurer les sauvegardes contenant des bases de données en mode lecture seule. Cette limitation sera supprimée sous peu.   
 
Pour plus d’informations sur les instructions de restauration, consultez [Instructions RESTORE](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql).

### <a name="service-broker"></a>Service broker 
 
- Le Service Broker entre instances n’est pas pris en charge 
 - `sys.routes` - Condition préalable : sélectionnez l’adresse à partir de sys.routes. L’adresse doit être LOCAL sur tous les itinéraires. Consultez [sys.routes](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-routes-transact-sql).
 - `CREATE ROUTE` -Vous ne pouvez pas `CREATE ROUTE` avec une `ADDRESS` autre que `LOCAL`. Consultez [CREATE ROUTE](https://docs.microsoft.com/sql/t-sql/statements/create-route-transact-sql).
 - `ALTER ROUTE` -Vous ne pouvez pas `ALTER ROUTE` avec une `ADDRESS` autre que `LOCAL`. Consultez [ALTER ROUTE](https://docs.microsoft.com/sql/t-sql/statements/alter-route-transact-sql).  
 
### <a name="service-key-and-service-master-key"></a>Clé de service et clé principale de service 
 
- [Backup master key](https://docs.microsoft.com/sql/t-sql/statements/backup-master-key-transact-sql) n’est pas pris en charge (géré par SQL Database service) 
- [Restore master key](https://docs.microsoft.com/sql/t-sql/statements/restore-master-key-transact-sql) n’est pas pris en charge (géré par SQL Database service) 
- [Backup service master key](https://docs.microsoft.com/sql/t-sql/statements/backup-service-master-key-transact-sql) n’est pas pris en charge (géré par SQL Database service) 
- [Restore service master key](https://docs.microsoft.com/sql/t-sql/statements/restore-service-master-key-transact-sql) n’est pas pris en charge (géré par SQL Database service) 
 
### <a name="stored-procedures-functions-triggers"></a>Procédures stockées, fonctions, déclencheurs 
 
- `NATIVE_COMPILATION` n’est actuellement pas pris en charge. 
- Les options [sp_configure](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-configure-transact-sql) suivantes ne sont pas prises en charge : 
 - `allow polybase export` 
 - `allow updates` 
 - `filestream_access_level` 
 - `max text repl size` 
 - `remote data archive` 
 - `remote proc trans` 
- `sp_execute_external_scripts` n’est pas pris en charge. Consultez [sp_execute_external_scripts](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql#examples).
- `xp_cmdshell` n’est pas pris en charge. Consultez [xp_cmdshell](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/xp-cmdshell-transact-sql).
- `Extended stored procedures` ne sont pas pris en charge, y compris `sp_addextendedproc` et `sp_dropextendedproc`. Consultez [Procédures stockées étendues](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/general-extended-stored-procedures-transact-sql)
- `sp_attach_db`, `sp_attach_single_file_db`, et `sp_detach_db` ne sont pas pris en charge. Consultez [sp_attach_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-attach-db-transact-sql), [sp_attach_single_file_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-attach-single-file-db-transact-sql) et [sp_detach_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-detach-db-transact-sql).
- `sp_renamedb` n’est pas pris en charge. Consultez [sp_renamedb](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-renamedb-transact-sql).

### <a name="sql-server-agent"></a>Agent SQL Server 
 
- Les paramètres de l’Agent SQL sont en lecture seule. La procédure `sp_set_agent_properties` n’est pas prise en charge dans Managed Instance.  
- Travaux - seules les étapes de travail T-SQL sont actuellement prises en charge (davantage d’étapes seront ajoutées au cours de la préversion publique).
 - SSIS n’est pas encore pris en charge. 
 - La réplication n’est pas encore prise en charge  
  - Le lecteur du journal des transactions n’est pas encore pris en charge.  
  - La capture instantanée n’est pas encore prise en charge.  
  - La base de données du serveur de distribution n’est pas encore prise en charge.  
  - La fusion n’est pas prise en charge.  
  - L’agent de lecture de la file d’attente n’est pas pris en charge.  
 - L’interface de commande n’est pas encore prise en charge. 
  - Managed Instance ne peut pas accéder aux ressources externes (par exemple, les partages réseau via robocopy).  
 - PowerShell n’est pas encore pris en charge.
 - Analysis Services ne sont pas pris en charge.  
- Les notifications sont partiellement prises en charge.
 - Les notifications par e-mail sont prises en charge, elles requièrent la configuration d’un profil de messagerie de base de données. Il ne peut y avoir qu’un seul profil de messagerie de base de données et il doit être appelé `AzureManagedInstance_dbmail_profile` en préversion publique (limitation temporaire).  
 - Les récepteurs de radiomessagerie ne sont pas pris en charge.  
 - NetSend n’est pas pris en charge. 
 - Les alertes ne sont pas encore prises en charge.
 - Les proxies ne sont pas pris en charge.  
- EventLog n’est pas pris en charge. 
 
Les fonctionnalités suivantes ne sont actuellement pas prises en charge, mais seront activées à l’avenir :  
- Proxies
- Planification de travaux sur une UC inactive 
- Activation/Désactivation de l’Agent
- Alertes

Pour plus d’informations sur SQL Server Agent, consultez [SQL Server Agent](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent).
 
### <a name="tables"></a>Tables 

Les éléments suivants ne sont pas pris en charge : 
- `FILESTREAM` 
- `FILETABLE` 
- `EXTERNAL TABLE` 
- `MEMORY_OPTIMIZED`  

Pour plus d’informations sur la création et modification des tables, consultez [CREATE TABLE](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql) et [ALTER TABLE](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql).
 
## <a name="Changes"></a> Changements de comportement 
 
Les variables, fonctions et vues suivantes retournent des résultats différents :  
- `SERVERPROPERTY('EngineEdition')` retourne la valeur 8. Cette propriété identifie Managed Instance de façon unique. Consultez [SERVERPROPERTY](https://docs.microsoft.com/sql/t-sql/functions/serverproperty-transact-sql).
- `SERVERPROPERTY('InstanceName')` retourne le nom d’instance court, par exemple, « myserver ». Consultez [SERVERPROPERTY(« InstanceName »)](https://docs.microsoft.com/sql/t-sql/functions/serverproperty-transact-sql).
- `@@SERVERNAME` retourne le nom DNS « connectable »complet, par exemple, my-managed-instance.wcus17662feb9ce98.database.windows.net. Consultez [@@SERVERNAME](https://docs.microsoft.com/sql/t-sql/functions/servername-transact-sql).  
- `SYS.SERVERS` -retourne le nom DNS « connectable »complet, tel que `myinstance.domain.database.windows.net` pour les propriétés « name » et « data_source ». Consultez [SYS. SERVERS](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-servers-transact-sql). 
- `@@SERVERNAME` Retourne le nom DNS « connectable » complet, tel que `my-managed-instance.wcus17662feb9ce98.database.windows.net`. Consultez [@@SERVERNAME](https://docs.microsoft.com/sql/t-sql/functions/servername-transact-sql).  
- `SYS.SERVERS` -retourne le nom DNS « connectable »complet, tel que `myinstance.domain.database.windows.net` pour les propriétés « name » et « data_source ». Consultez [SYS. SERVERS](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-servers-transact-sql). 
- `@@SERVICENAME` retourne la valeur NULL, car il n’a pas de sens dans l’environnement Managed Instance. Consultez [@@SERVICENAME](https://docs.microsoft.com/sql/t-sql/functions/servicename-transact-sql).   
- `SUSER_ID` est pris en charge. Retourne NULL si le compte de connexion AAD n’est pas dans sys.syslogins. Consultez [SUSER_ID](https://docs.microsoft.com/sql/t-sql/functions/suser-id-transact-sql).  
- `SUSER_SID` n’est pas pris en charge. Retourne des données incorrectes (problème temporaire connu). Consultez [SUSER_SID](https://docs.microsoft.com/sql/t-sql/functions/suser-sid-transact-sql). 
- `GETDATE()` et autres fonctions de date et d’heure intégrées retourne toujours l’heure dans le fuseau horaire UTC. Consultez [GETDATE](https://docs.microsoft.com/sql/t-sql/functions/getdate-transact-sql).

## <a name="Issues"></a> Problèmes connus et limitations

### <a name="tempdb-size"></a>Taille de TEMPDB

`tempdb` est divisé en 12 fichiers avec une taille maximale de 14 Go par fichier. Cette taille maximale par fichier ne peut pas être modifiée et de nouveaux fichiers ne peuvent pas être ajoutés à `tempdb`. Cette limitation sera supprimée sous peu. Certaines requêtes peuvent retourner une erreur si elles ont besoin de plus de 168 Go dans `tempdb`.

### <a name="exceeding-storage-space-with-small-database-files"></a>Dépassement de l’espace de stockage avec des fichiers de base de données de petite taille

Chaque instance gérée a jusqu’à 35 To de stockage réservé pour l’espace disque Premium Azure et chaque fichier de bases de données est placé sur un disque physique séparé. Les tailles de disque peuvent être de 128 Go, 256 Go, 512 Go, 1 To ou 4 To. L’espace non utilisé sur le disque n’est pas facturé, mais la somme des tailles des disques Premium Azure ne peut pas dépasser 35 To. Dans certains cas, une instance gérée qui n’a pas besoin de 8 To au total peut dépasser la limite de 35 To Azure sur la taille de stockage, en raison d’une fragmentation interne. 

Par exemple, une instance gérée peut avoir un seul fichier de 1,2 To qui utilise un disque de 4 To et 248 fichiers de 1 Go chacun placé sur 248 disques de 128 Go. Dans cet exemple, la taille du stockage total du disque est 1 x 4 To + 248 x 128 Go = 35 To. Toutefois, la taille totale des instances réservées pour les bases de données est 1 x 1,2 To + 248 x 1 Go = 1,4 To. Cet exemple illustre que dans certaines circonstances, en raison d’une distribution très spécifique des fichiers, une Instance gérée peut atteindre la limite de stockage des disques Premium Azure lorsque vous vous y attendez le moins. 

Il n’y aura aucune erreur sur les bases de données existantes et elles peuvent croître sans aucun problème si de nouveaux fichiers ne sont pas ajoutés, mais les nouvelles bases de données ne peuvent pas être créées ni restaurées, car il n’y a pas suffisamment d’espace pour les nouveaux lecteurs de disques, même si la taille totale de toutes les bases de données n’atteint pas la limite de la taille de l’instance. L’erreur retournée dans ce cas n’est pas claire.

### <a name="incorrect-configuration-of-sas-key-during-database-restore"></a>Configuration incorrecte de la clé SAP au cours d’une restauration de la base de données

Il se peut que `RESTORE DATABASE` qui lit le fichier .bak réessaie constamment de lire le fichier .bak et retourne une erreur après une longue période si la signature d’accès partagé dans `CREDENTIAL` est incorrecte. Exécutez RESTORE HEADERONLY avant de restaurer une base de données pour vous assurer que la clé SAP est correcte.
Assurez-vous que vous supprimez le `?` de début de la clé SAP générée à l’aide du portail Azure.

### <a name="tooling"></a>Outils

SQL Server Management Studio et SQL Server Data Tools peuvent rencontrer des problèmes lors de l’accès à Managed Instance. Tous les problèmes d’outils seront traités avant la mise à disposition générale.

### <a name="incorrect-database-names"></a>Noms de base de données incorrects

Managed Instance peut afficher la valeur GUID au lieu du nom de la base de données pendant la restauration ou dans certains messages d’erreur. Ces problèmes seront corrigés avant la mise à disposition générale.

### <a name="database-mail-profile"></a>Profil de messagerie de base de données
Il ne peut y avoir qu’un seul profil de messagerie de base de données et il doit être appelé `AzureManagedInstance_dbmail_profile`. Il s’agit d’une limitation temporaire qui sera supprimée prochainement.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur Managed Instance, consultez [What is a Managed Instance?](sql-database-managed-instance.md) (Présentation de l’option Managed Instance)
- Pour consulter la liste des fonctionnalités et les comparer, consultez [Fonctionnalités SQL communes](sql-database-features.md).
- Pour suivre un didacticiel, consultez [Créer une option Managed Instance](sql-database-managed-instance-tutorial-portal.md).
